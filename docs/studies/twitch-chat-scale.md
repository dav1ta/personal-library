# 🔥 Twitch – Scaling Chat to Millions

## 1. Background

Twitch’s platform combines live video with a massive real-time chat system:

- Millions of viewers connect concurrently across many channels.
- Popular streams can produce tens of thousands of messages per second in a single chat.
- Chat must feel “live” — messages should appear with very low latency.
- The system must tolerate spikes (e.g., esports events, major announcements).

Initially, Twitch used an IRC-based backend to power chat. As usage grew, this legacy architecture began to struggle.

## 2. Problem

From all.md:

- Problem: IRC-based chat backend choking on massive fan-out.
- Cause: Too many channels + too many messages per second.
- Solution:
  - Built multi-layered fan-out architecture.
  - Used Golang for chat servers.
  - Implemented sharding by channel ID.
  - Wrote a custom TCP load-balancer tuned for short messages.
- Impact: Sustains millions of msgs/sec globally.

In effect:

- The legacy IRC stack hit CPU, memory, and I/O limits.
- Single nodes ran too many channels and connections.
- Fan-out (sending each message to all interested clients) became a core bottleneck.

## 3. Root Causes

### 3.1 Limitations of the IRC-based backend

IRC:

- Was designed decades ago for much smaller-scale chat.
- Assumes a certain model of channels and servers that doesn’t easily generalize to millions of concurrent users and channels.
- Lacked built-in mechanisms for efficient large-scale fan-out and sharding.

As Twitch grew:

- The IRC compatibility layer added overhead.
- Scaling required increasingly complex workarounds.

### 3.2 Monolithic fan-out paths

In the original setup:

- A given server might handle a large number of channels and connected clients.
- Each incoming message had to be delivered to all subscribers, often in a single tier.

This resulted in:

- High per-message CPU and I/O costs.
- Poor scaling as message rate and channel count increased.
- Risk that hot channels could dominate resources on a node.

### 3.3 Generic load balancing for non-generic traffic

Standard load balancers:

- Are typically tuned for HTTP-style request/response traffic.
- Not necessarily optimized for long-lived TCP connections carrying small chat messages.

Issues:

- Connection distribution that didn’t align well with chat-specific sharding needs.
- Inefficiencies in handling thousands of small messages across many open sockets.

## 4. Architecture Before (Simplified)

Conceptually:

```text
Clients (IRC/WS)
   │
   ▼
┌───────────────────────┐
│ IRC-Based Chat Server │
│  - Channel state      │
│  - User presence      │
│  - Message fan-out    │
└───────────┬───────────┘
            │
            ▼
       Generic Load Balancer
```

Characteristics:

- IRC-based server process handling many channels and clients.
- Single-tier fan-out for each message.
- Generic load balancer not specialized for persistent chat connections.

## 5. Architecture After (Optimized)

Twitch re-architected chat with:

- A **multi-layered fan-out** design.
- **Golang** chat servers optimized for long-lived connections and small messages.
- **Sharding by channel ID** to distribute load.
- A **custom TCP load balancer** tuned for chat traffic.

Conceptual view:

```text
             ┌───────────────────────┐
Clients  →   │ Custom TCP Load Bal. │
             └───────────┬──────────┘
                         │
          ┌──────────────┴───────────────┐
          ▼                              ▼
┌───────────────────────┐       ┌───────────────────────┐
│ Go Chat Frontend Shard│  ...  │ Go Chat Frontend Shard│
│  - WS/TCP termination │       │  - WS/TCP termination │
│  - Per-channel routing│       │  - Per-channel routing│
└───────────┬───────────┘       └───────────┬───────────┘
            │                               │
            └───── Multi-layer fan-out ─────┘
```

## 6. How They Implemented the Fix (Step by Step)

### 6.1 Adopt Golang for chat servers

Motivations for Go:

- Efficient concurrency via goroutines and channels.
- Good fit for network servers with many long-lived connections.
- Predictable performance and modest memory footprint.

Implementation ideas:

- Each Go chat server maintains thousands of WebSocket/TCP connections.
- Lightweight goroutines handle IO and message routing.
- Shared internal structures map channels to connected clients.

Benefits:

- Better utilization of CPU and memory per node.
- Simplified deployment of stateless or lightly stateful chat servers.
- Language/runtime more suited to modern network services than the legacy IRC code.

### 6.2 Shard by channel ID

Key insight:

- Chat load is naturally partitioned by channel.
- Messages for a given channel only need to go to viewers of that channel.

Sharding strategy:

- Hash or map each channel ID to a specific shard (or group of shards).
- Ensure that all clients for a given channel connect to the same shard or shard group.

Effects:

- Each shard handles only a subset of channels and their messages.
- Hot channels can be isolated and spread across shards as needed.
- Load becomes more predictable and controllable.

### 6.3 Multi-layered fan-out architecture

Instead of a single-tier fan-out:

- Introduce multiple layers responsible for different aspects of routing and delivery.

Example layering:

1. **Ingress / edge**:

   - Terminates TCP/WebSocket connections.
   - Authenticates users and assigns them to channel shards.

2. **Channel shard layer**:

   - Maintains membership lists for channels.
   - Receives messages from publishers (clients or upstream services).
   - Determines target subscribers.

3. **Per-node fan-out**:

   - Efficiently writes messages to local client connections.
   - Uses optimized data paths for small, frequent messages.

This approach:

- Limits the scope of fan-out work per node.
- Allows horizontal scaling by adding more shards and nodes.
- Enables targeted optimizations at each layer (routing, batching, writing).

### 6.4 Custom TCP load-balancer tuned for chat

Twitch built a custom TCP load balancer to:

- Manage long-lived chat connections effectively.
- Distribute connections to shards according to channel and capacity.
- Optimize for small messages and high connection counts.

Key considerations:

- Awareness of channel/shard mapping to avoid unnecessary cross-node hops.
- Efficient handling of large numbers of open sockets.
- Fine-grained control over routing policy (e.g., sticky sessions by channel).

Benefits:

- More predictable distribution of load across chat servers.
- Reduced overhead compared to generic L4/L7 load balancers designed for HTTP.
- Better resilience to sudden spikes in connections for specific channels.

### 6.5 Optimizations in message handling

Within each Go chat server:

- Avoid unnecessary allocations: reuse buffers for message serialization.
- Implement batching where possible:

  - Combine multiple messages or writes for the same client or socket.
  - Reduce number of syscalls by sending data in chunks.

- Tune TCP settings (e.g., disable Nagle’s algorithm) to minimize latency for small messages.

Combined, these optimizations reduce CPU and system call overhead per message, enabling higher throughput.

## 7. Performance Results

From the original summary:

- Impact: Sustains millions of msgs/sec globally.

This reflects:

- A system capable of handling global chat volume across many channels and events.
- Capacity to serve both “slow” chats and extremely active channels without collapsing.
- Stable latency for chat messages even during peak events.

## 8. Lessons and Reusable Patterns

### 8.1 Design chat systems around channels and shards

- Use natural keys (channel IDs) to shard state and traffic.
- Keep channel membership and message routing local to shards where possible.

### 8.2 Use runtimes suited to long-lived connections

- Languages like Go are well-suited to high-connection, message-driven servers.
- Avoid architectures that don’t map cleanly onto modern concurrency and IO models.

### 8.3 Build multi-layered fan-out

- Don’t rely on a single tier to handle fan-out for all messages.
- Split responsibilities: ingress, routing, shard-level fan-out.

### 8.4 Tune load balancing for the traffic type

- Generic load balancers may not be ideal for chat.
- Custom or specialized load balancers can be optimized for many persistent TCP/WS connections and small messages.

### 8.5 Optimize for both message rate and connection count

- Architect for huge numbers of idle or semi-idle connections.
- Simultaneously handle high message rates in hot channels.

These patterns apply to any large-scale, real-time messaging system: gaming chat, collaboration tools, live events, or any platform where huge numbers of users interact in channels in real time.

Next: [Uber Matching Engine](uber-matching-engine.md)
