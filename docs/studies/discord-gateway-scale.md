# 🔥 Discord – Scaling to Millions of WebSocket Connections

## 1. Background

Discord’s real-time system powers:

- Persistent WebSocket connections from millions of clients.
- Bi-directional messaging: presence updates, typing indicators, chat messages, voice signaling.
- Fan-out of events to many interested clients (e.g., a large guild with tens or hundreds of thousands of members).

The “gateway” is the layer that:

- Terminates WebSockets.
- Authenticates and identifies clients.
- Receives events from backend services.
- Pushes those events to connected clients.

As Discord grew, the original gateway stack began to hit CPU and connection limits.

## 2. Problem

From all.md:

- Problem: Gateway cluster hitting CPU and connection limits.
- Cause: Erlang node couldn’t efficiently multiplex millions of WS clients.
- Solution:
  - Migrated gateway to Rust using Tokio.
  - Implemented sharded architecture for each guild (server).
  - Disabled Nagle and optimized TCP settings.
  - Built a batched event dispatcher to reduce fan-out cost.
- Impact: Handled millions of concurrent connections per machine.

More concretely, the issues were:

- High CPU utilization per node at relatively “low” connection counts by Discord’s standards.
- Difficulty scaling a single Erlang node or configuration to handle millions of concurrent WebSockets.
- Inefficient fan-out when the same event had to be sent to many clients (e.g., presence updates).
- Latency and reliability concerns when operating close to resource limits.

## 3. Root Causes

### 3.1 Limits of the existing runtime

The original implementation used Erlang/BEAM:

- Erlang is strong for concurrency and fault tolerance, but the chosen architecture and implementation hit practical limits when managing millions of WebSockets per node.
- Per-connection overhead and scheduling behavior became a bottleneck at Discord’s scale.
- Tuning and pushing the VM became increasingly complex.

### 3.2 Per-connection overhead

Managing each WebSocket connection involved:

- Bookkeeping for heartbeats, timeouts, and backpressure.
- Serialization and buffering for outgoing payloads.
- Coordination for presence and guild membership.

At high connection counts, even small per-connection costs add up.

### 3.3 Inefficient fan-out

Certain events needed to be sent to many clients:

- Presence updates (user online/offline/idle).
- Guild configuration changes.
- Channel and role updates.

If the system handled fan-out naïvely (per-connection work for each event), CPU and bandwidth usage exploded.

### 3.4 TCP and kernel tuning

- Default TCP settings (e.g., Nagle’s algorithm) can introduce latency for small, frequent messages.
- Without careful tuning, kernel buffers, socket options, and scheduler behavior limit throughput.

## 4. Architecture Before (Simplified)

A conceptual view of the earlier gateway:

```text
Clients (WS)
   │
   ▼
┌───────────────────────┐
│ Erlang Gateway Nodes  │
│  - WS termination     │
│  - Auth/identify      │
│  - Event dispatch     │
└───────────────────────┘
   │
   ▼
Backend services (chat, presence, etc.)
```

Characteristics:

- Gateway nodes implemented in Erlang.
- Scaling horizontally required many nodes as traffic grew.
- Per-node concurrency and CPU utilization became limiting factors.

## 5. Architecture After (Optimized)

Discord migrated the gateway to a Rust/Tokio-based architecture with sharding:

```text
                ┌────────────────────────────┐
Clients (WS) →  │ Rust Gateway Shard N      │
                │  - WS termination         │
                │  - Guild-based sharding   │
                │  - Batched event dispatch │
                └────────────────────────────┘
                    ▲      ▲       ▲
                    │      │       │
                    └──────┴───────┘
                      Backend event streams
```

Key ideas:

- Use Rust + Tokio for highly efficient async IO and concurrency.
- Divide the logical universe (guilds/servers) into shards.
- Each shard is responsible for a subset of guilds and their connections.
- Events are batched and dispatched efficiently to many clients.

## 6. How They Implemented the Fix (Step by Step)

### 6.1 Choose Rust + Tokio for the new gateway

Rust:

- Provides memory safety without a garbage collector.
- Allows fine-grained control over allocation and data structures.
- Compiles to efficient native code.

Tokio:

- High-performance async runtime based on epoll/kqueue.
- Scales to large numbers of sockets with minimal overhead.

Benefits:

- Support millions of concurrent connections with predictable memory and CPU usage.
- Avoid runtime GC pauses and interpreter overhead.

### 6.2 Introduce sharded architecture by guild

Discord’s data model has a natural sharding key: the guild (server). The new gateway:

- Divides guilds into shards (e.g., shard 0 handles guild IDs in a certain range).
- Each shard:

  - Manages only the connections for its assigned guilds.
  - Receives events relevant to those guilds.
  - Maintains in-memory state (presence, metadata) for its subset.

Effects:

- Load is spread more evenly across shards/nodes.
- No single node must track the entire global state.
- Vertical scaling: each shard can be tuned to handle a large number of connections.

### 6.3 Efficient WebSocket management

Using Rust/Tokio:

- Each WebSocket connection is handled by an async task rather than a heavyweight thread.
- Connections share a small number of reactor threads.
- Backpressure is integrated: if a client falls behind, its send buffer is controlled.

Patterns:

- Use compact, fixed-size structures for connection state.
- Avoid unnecessary allocations in hot paths (e.g., reuse buffers).
- Move heavy work (encoding, compression) off the critical path when possible.

### 6.4 Disable Nagle’s algorithm and tune TCP

To reduce latency for small messages:

- Nagle’s algorithm, which batches small TCP packets to improve efficiency, was disabled (`TCP_NODELAY`).
- This avoids waiting to accumulate data before sending, which is important for interactive events like typing indicators and presence updates.

TCP tuning also included:

- Adjusting socket buffer sizes.
- Tweaking kernel parameters (e.g., ephemeral port ranges, backlog sizes).
- Ensuring adequate file descriptor limits.

These changes reduced queuing and improved responsiveness.

### 6.5 Build a batched event dispatcher

Instead of sending events one-by-one to each connection:

- The gateway groups events destined to many clients (e.g., in the same guild).
- Performs per-event work once (serialization, compression) and then reuses results across recipients when possible.
- Pushes updates in batches to the underlying IO layer.

Benefits:

- Lower CPU cost per recipient.
- Fewer system calls and context switches.
- Better network utilization.

Conceptual flow:

1. Backend emits an event for a guild.
2. The responsible shard receives it.
3. Shard determines the list of interested connections.
4. Event is serialized once (or a small number of times).
5. The serialized payload is queued to many sockets in an efficient batch.

### 6.6 Scale out and balance shards

Deployment changes:

- Horizontal scaling by adding more shards/nodes.
- Sharding logic ensures deterministic routing of guilds to shards.
- Control plane manages shard placement, failover, and rebalancing.

Operational benefits:

- Predictable capacity per shard.
- Ability to scale gradually with traffic growth.
- Resilience: individual shard failures don’t bring down the entire system.

## 7. Performance Results

From the original summary:

- Impact: Handled millions of concurrent connections per machine.

Implications:

- Higher connection density per node reduces hardware costs.
- Lower per-connection overhead improves both latency and throughput.
- System has more headroom for events and spikes in activity.

## 8. Lessons and Reusable Patterns

### 8.1 Match runtime to scale

- At extreme concurrency levels, choice of runtime (language + async IO model) is critical.
- Rust + Tokio (or similar) can support large numbers of connections with predictable performance.

### 8.2 Shard using natural keys

- Use domain-specific keys (e.g., guild/server) to partition load.
- Each shard holds a subset of state and connections, simplifying scaling.

### 8.3 Tune TCP for your traffic pattern

- Disable Nagle’s algorithm when you care more about latency for small messages than raw throughput.
- Adjust socket and kernel settings to avoid subtle bottlenecks.

### 8.4 Batch where possible

- Batch event handling: serialize once, reuse across many recipients.
- Batch syscalls to reduce kernel overhead.

### 8.5 Architect for both connection count and fan-out

- Design both for huge numbers of idle connections and high fan-out patterns.
- Separate concerns: connection management, routing, and event fan-out.

### 8.6 Migrate incrementally

- Introduce the new gateway alongside the old one.
- Gradually move traffic and compare metrics.
- Roll back easily if regressions occur.

These patterns apply to any large-scale, real-time system with many long-lived connections, such as chat platforms, game backends, or collaboration tools.

Next: [Figma CRDT Multiplayer](figma-crdt-multiplayer.md)
