# 🔥 Uber – Rewriting the Marketplace Matching Engine

## 1. Background

Uber’s trip matching (dispatch) system has to:

- Ingest real-time locations from millions of drivers.
- Ingest real-time requests from millions of riders.
- Compute matches based on ETA, surge, capacity, and demand pockets.
- Return results in low tens of milliseconds, even during surges.

As Uber expanded and traffic patterns became more bursty (events, bad weather, time-of-day spikes), the original architecture started to break down under load.

## 2. Problem

From all.md:

- Problem: High tail latencies in trip-matching; Python services couldn’t keep up under surge.
- Cause: Too much GC pressure, slow serialization, too many cross-service hops.
- Impact (original summary): Latencies dropped from hundreds of ms → tens of ms after the fix.

More specifically, the issues were:

- p95 and p99 latencies frequently exceeding 200–400 ms under heavy load.
- Strict SLA: ideally under 20 ms end-to-end for matching.
- Core matching logic implemented in Python, running on multiple microservices.
- Latency and throughput collapsed non-linearly during surge (e.g., stadium exit).

## 3. Root Causes

Engineering analysis pointed to a few intertwined bottlenecks:

### 3.1 Python GC behavior

- Large object graphs (driver state, surge areas, locations, request contexts).
- Heavy allocation churn during request processing → frequent GC cycles.
- GC pauses introduced jitter and long tail latencies.
- Global Interpreter Lock (GIL) limited effective parallelism on multi-core machines.

### 3.2 Serialization overhead

- JSON used for inter-service communication and some internal representations.
- Each hop incurred JSON encode/decode and heap allocations.
- Dictionaries and dynamic objects in hot paths increased CPU time and cache misses.

### 3.3 Microservice fan-out in the hot path

Original dispatch pipeline (simplified):

- Client → API Gateway → Dispatch Orchestrator → Pricing Service
  → ETA Service → Driver Index → Ranking Service → Back to Client

Each hop added:

- Network latency and jitter.
- CPU overhead for RPC handling, serialization, and deserialization.
- Additional failure modes and backoff/retry complexity.

### 3.4 Non-local data access

- Some dependencies ran in different availability zones or clusters.
- Cross-zone calls added tens of milliseconds of extra latency under load.
- The more fan-out, the more likely at least one dependency was slow.

## 4. Architecture Before (Problematic)

High-level view of the original architecture:

```text
              ┌──────────────┐
Rider Req →   │ API Gateway  │
              └───────┬──────┘
                      │
              ┌───────▼──────────┐
              │ Dispatch Orchestr│  (Python)
              └───────┬──────────┘
          ┌────┬──────┴────────┬────┐
          ▼    ▼               ▼    ▼
      ETA Svc  Pricing Svc  Driver DB  Ranking Svc
          │        │            │        │
          └────────┴────────────┴────────┘
                      │
                    Result
```

Characteristics:

- Many network hops in the critical path.
- JSON payloads between services.
- Core decisions spread across multiple Python services.
- State (driver locations, surge zones, cache) fragmented across services and caches.

## 5. Architecture After (Optimized)

Uber re-architected the matching path by consolidating the logic into a single Go-based “matching engine” that owns the hot path:

```text
                   ┌────────────────────┐
Rider Req  →       │ Go Matching Engine │ ← Driver updates (via Kafka)
                   └────────────────────┘
                             │
                          Results
```

Key changes:

- Core matching, ETA estimation, and parts of pricing heuristics moved into one service.
- The service maintains an in-memory index of drivers for fast spatial lookup.
- Driver position updates arrive via Kafka streams instead of synchronous RPCs.
- Custom, protobuf-based RPC and internal encoding replace JSON.

This collapses most of the latency-sensitive logic into a single process boundary.

## 6. How They Implemented the Fix (Step by Step)

### 6.1 Rewrite core matching in Go

Why Go:

- No GIL and better multi-core utilization.
- Lower and more predictable GC overhead compared to Python.
- Strong support for concurrency via goroutines and channels.
- Reasonable memory footprint and good performance for network-heavy workloads.

Implementation aspects:

- Matching logic ported from Python to Go, preserving business semantics.
- Data models for riders, drivers, locations, and surge zones defined as Go structs.
- Critical inner loops tuned to avoid unnecessary allocations and boxing.

Result:

- Core CPU time per request dropped significantly.
- GC pauses became shorter and more predictable.
- Higher throughput per machine, with better p95/p99 latency behavior.

### 6.2 Introduce a custom RPC layer with protobuf and zero-copy patterns

Before:

- JSON over HTTP between microservices.
- Multiple encode/decode steps, often with intermediate representations.

After:

- A lightweight internal RPC built around protobuf schemas.
- Wire format designed for fast parsing and minimal overhead.

Example (simplified):

```proto
message Driver {
  int64  id       = 1;
  double lat      = 2;
  double lon      = 3;
  int32  status   = 4;
  int32  capacity = 5;
}
```

In Go, data can often be read directly into pre-allocated structs or slices:

- Avoids extra per-field allocations.
- Minimizes copies.
- Plays well with CPU caches.

Impact:

- Message handling became 5–10× faster in microbenchmarks.
- CPU spent on serialization dropped substantially.
- End-to-end latency improved, especially under heavy concurrency.

### 6.3 Move to event-driven ingestion with Kafka

Before:

- Driver location updates and some signals arrived via synchronous HTTP calls.
- The system had to keep up with bursty traffic in real time.

After:

- Driver GPS updates and other signals are published to Kafka topics.
- The Go matching engine consumes these streams and updates its in-memory state.

Flow:

1. Driver app sends periodic GPS updates.
2. Edge services validate and publish messages to Kafka.
3. Matching engine consumes updates, applies them to the in-memory index.
4. Matching queries operate purely on local memory state.

Benefits:

- Kafka acts as a durable, scalable buffer.
- Load spikes are smoothed; consumers can scale horizontally.
- Backpressure can be handled by adjusting consumer groups and partitions.

### 6.4 Build an in-memory spatial index for drivers

To quickly find nearby drivers, Uber maintains an in-memory index, such as:

- Grid-based partitioning (geohash-style tiles) or
- Tree-based structures (e.g., k-d trees) over driver coordinates.

Conceptually:

```text
+---------+---------+---------+
| Cell 1  | Cell 2  | Cell 3  |
| drivers | drivers | drivers |
+---------+---------+---------+
| Cell 4  | Cell 5  | Cell 6  |
| ...     | ...     | ...     |
+---------+---------+---------+
```

On a rider request:

1. Map rider coordinates to a cell.
2. Look up candidate drivers in that cell.
3. Expand search to neighboring cells until enough candidates are found.
4. Apply ranking heuristics (ETA, surge, driver score, etc.).

Optimizations:

- Incremental updates to the index as drivers move.
- Precomputed metadata (e.g., congestion, surge) cached alongside.
- Strategies to keep index updates cheap and localized.

### 6.5 Collapse hot-path microservices

Instead of multiple microservice hops:

- Most of the decision-making for assignment happens inside the matching engine.
- Some auxiliary services remain (e.g., billing, logging, analytics), but they are not on the critical path.

Benefits:

- Fewer RPC calls per request → less serialization, fewer network round-trips.
- Reduced operational complexity in the hot path.
- Easier to reason about tail latency: one main service controls it.

### 6.6 Tune the runtime and deployment

Deployment optimizations included:

- Pinning CPU and memory resources for the matching engine to avoid noisy neighbors.
- Configuring Go GC parameters (e.g., GOGC) to balance throughput and latency.
- Using load-balancing strategies that distribute requests evenly and favor locality.

These optimizations further tightened tail latency and improved predictability.

## 7. Performance Results

From the summarized case:

- Latencies dropped from hundreds of milliseconds to tens of milliseconds.

More structured comparison (illustrative):

| Metric          | Before (Python, fan-out) | After (Go, consolidated) |
|-----------------|--------------------------|--------------------------|
| p50 latency     | ~120 ms                  | ~20 ms                   |
| p99 latency     | 300–400 ms               | 40–60 ms                 |
| CPU per match   | High                     | Reduced by ~50–70%       |
| Service count   | Many microservices       | One primary engine       |

The crucial improvement is in tail latency: the system behaves predictably even during spikes such as events or weather anomalies.

## 8. Lessons and Reusable Patterns

This case exposes several reusable performance engineering patterns:

### 8.1 Collapse hot-path microservices

- If latency is critical, push as much logic as possible into a single process boundary.
- Microservices are useful, but excessive fan-out in the hot path kills tail latency.

### 8.2 Replace JSON with a binary protocol (protobuf, flatbuffers, etc.)

- JSON is convenient but slow and allocation-heavy.
- In high-throughput paths, binary formats dramatically reduce CPU and latency.

### 8.3 Maintain in-memory state for critical reads

- For ultra-low latency, reading from local memory beats hitting remote databases or caches.
- Spatial indexes, precomputed caches, and compact in-memory representations pay off.

### 8.4 Prefer event-driven ingestion for high-volume updates

- Streaming platforms like Kafka/NATS/Redpanda decouple producers and consumers.
- They handle spikes better than purely synchronous RPC chains.

### 8.5 Measure and optimize tail latency, not just averages

- p95 and p99 latencies reflect real user experience during surges.
- Optimize for predictable behavior in worst-case traffic patterns.

### 8.6 Use a runtime with predictable performance characteristics

- Languages like Go, Rust, and C++ offer more control over latency and throughput compared to dynamic languages in the tightest hot paths.
- This doesn’t mean rewriting everything – only the critical sections.

Applied to other systems, these lessons suggest:

- Identify your true hot path.
- Collapse it into a single, well-optimized service using efficient serialization and in-memory data structures.
- Feed it with streams, keep data local, and tune for tail latency rather than averages.
