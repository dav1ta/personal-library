# 🔥 Netflix – Solving Java GC Pauses in High-Traffic Streams

## 1. Background

Netflix’s streaming platform relies on a fleet of services that:

- Ingest and process user viewing activity.
- Consume data streams (often via Kafka) to drive personalization, recommendations, and analytics.
- Operate under continuously high throughput with tight SLAs, since delays can affect user experience (e.g., adaptive bitrate decisions, session tracking).

Some of these services were Java-based Kafka consumers handling high-volume event streams.

## 2. Problem

From all.md:

- Problem: Kafka consumer services had unpredictable GC pauses → video stutters.
- Cause: Large heap + allocation-heavy deserialization code.
- Solution:
  - Moved to off-heap buffers with Netty.
  - Switched from Jackson to protobuf with pre-allocated objects.
  - Tuned GC to use G1 with small regions.
  - Added circuit breakers for backpressure.
- Impact: GC pauses dropped by ~90%.

In practice, this meant:

- Kafka consumers occasionally stopped the world for long GC pauses.
- Downstream processing lagged, causing buffering issues and delayed reactions.
- Latency-sensitive components experienced jitter that impacted perceived quality of service.

## 3. Root Causes

### 3.1 Large heap with allocation-heavy deserialization

The original design used:

- JSON deserialization via libraries like Jackson.
- Creation of many short-lived objects per message:
  - Intermediate maps, lists, and POJOs.
  - Extra wrappers for validation and transformation.

On a large heap, this caused:

- Frequent young-generation collections.
- Occasional long full GC cycles.
- CPU time diverted from actual work to GC.

### 3.2 Object churn and memory fragmentation

Each message:

- Allocated new objects for payload fields.
- Sometimes copied data multiple times (raw bytes → string → parsed object).

Over time:

- The heap became fragmented.
- GC had to work harder to compact and free memory.

### 3.3 Inadequate backpressure and flow control

When upstream load spiked:

- Consumers tried to keep up, increasing heap usage and allocation rates.
- GC struggled to reclaim enough space quickly.
- Latency spikes and pauses cascaded through the pipeline.

Without clear backpressure, systems can enter a state where they are both overloaded and inefficient.

## 4. Architecture Before (Simplified)

A simplified view of a Kafka consumer service:

```text
Kafka Topic
    │
    ▼
┌───────────────────────┐
│ Java Consumer Service │
│  - Poll messages      │
│  - JSON (Jackson)     │
│  - Transform/process  │
│  - Write to downstream│
└───────────────────────┘
```

Characteristics:

- Messages deserialized from byte[] to JSON to domain objects.
- Per-message heap allocations.
- Large JVM heap configured to handle bursts.
- GC tuned, but still constrained by object churn and heap size.

## 5. Architecture After (Optimized)

The optimized pipeline focused on:

- Off-heap buffers managed by Netty.
- Compact binary serialization via protobuf.
- Pre-allocated objects and object pools.
- GC tuned using G1 with smaller regions.
- Circuit breakers enforcing backpressure.

Conceptually:

```text
Kafka Topic
    │
    ▼
┌─────────────────────────────────────────┐
│ Java/Netty Consumer Service            │
│  - Poll messages                       │
│  - Off-heap Netty ByteBuf              │
│  - Protobuf decode into reused objects │
│  - Process & forward                   │
│  - Circuit breakers / backpressure     │
└─────────────────────────────────────────┘
```

The key shift is that most heavy data handling moves off-heap or into reused structures, reducing GC pressure.

## 6. How They Implemented the Fix (Step by Step)

### 6.1 Introduce Netty and off-heap buffers

Netty provides:

- Efficient network IO abstractions.
- `ByteBuf` structures that can live off-heap.
- Pooled buffers to avoid frequent allocations.

Steps:

1. Replace naive `byte[]` handling with Netty `ByteBuf`s.
2. Configure pooled buffers so repeated allocations reuse existing memory blocks.
3. Ensure proper reference counting and lifecycle management of buffers.

Effect:

- Less on-heap data for raw message bytes.
- GC sees fewer large arrays and transient buffers.
- Better locality and control over memory.

### 6.2 Move from JSON (Jackson) to protobuf

JSON pros:

- Human-readable, flexible.

JSON cons in this context:

- Heavier parsing cost.
- More allocations (strings, maps, intermediate nodes).

Switching to protobuf:

- Fixed, compact binary layout.
- Generated code for reading/writing messages.
- Fewer allocations and less CPU per message.

Conceptual change:

- Define stable `.proto` schemas for Kafka payloads.
- Generate Java classes for producers and consumers.
- Replace JSON parsing with protobuf parsing directly from `ByteBuf` or `byte[]`.

Result:

- Lower CPU utilization.
- More predictable per-message cost.
- Less GC pressure from deserialization.

### 6.3 Use pre-allocated objects and pools

Instead of creating new objects per message:

- Maintain pools of reusable message objects or DTOs.
- Reuse instances by resetting fields between messages.

Patterns:

- Simple object pools backed by queues.
- Careful design to avoid concurrency issues (e.g., per-thread pools).
- Avoid storing pooled instances in global structures to prevent leaks.

Impact:

- Reduced allocation rate.
- Smoother GC behavior.
- Lower fragmentation of the heap.

### 6.4 Tune GC: G1 with small regions

G1 (Garbage-First) is suited for:

- Large heaps.
- Predictable pause times.

Tuning involved:

- Using G1 as the GC algorithm.
- Configuring smaller regions to make collections more incremental.
- Adjusting pause-time goals in line with SLA.
- Monitoring GC logs to validate improvements.

Effect:

- Collections became more frequent but shorter.
- Large, unpredictable pauses were greatly reduced.

### 6.5 Add circuit breakers and backpressure

To protect the system from overload:

- Circuit breakers measure processing latency, queue depths, or error rates.
- When limits are exceeded, they:
  - Slow or stop consumption.
  - Shed load in a controlled manner.
  - Signal upstream services or operators.

Backpressure strategies:

- Throttle Kafka polling if internal queues are backing up.
- Deprioritize non-critical work during spikes.
- Fail fast instead of letting latency grow unbounded.

Result:

- The system stays within safe operating limits.
- GC and CPU do not spiral out of control under sudden spikes.

## 7. Performance Results

From the original summary:

- GC pauses dropped by around 90%.

Practically, this translates to:

- Much fewer and shorter stop-the-world events.
- A smoother, more predictable processing pipeline.
- Better adherence to SLAs for streams that impact user experience.

## 8. Lessons and Reusable Patterns

### 8.1 Reduce on-heap allocations in hot paths

- Use off-heap buffers where appropriate.
- Reuse objects and buffers instead of constantly allocating new ones.

### 8.2 Prefer compact binary formats for high-volume systems

- Protobuf/Avro/FlatBuffers can dramatically reduce parsing and allocation overhead.
- Choose a schema-first approach for data traveling through high-throughput pipelines.

### 8.3 Tune GC with the workload in mind

- Choose a collector suited to large heaps and latency constraints (e.g., G1).
- Use GC logs and metrics to guide tuning, not guesses.

### 8.4 Add backpressure rather than over-provisioning

- Circuit breakers and throttling allow the system to degrade gracefully.
- It is better to reject or delay work than to let latency explode unpredictably.

### 8.5 Consider the full message lifecycle

- Network read → buffer → parse → process → write.
- Optimize each stage, especially transitions between heap and off-heap.

These principles are widely applicable to any JVM-based, high-throughput streaming system, not just Netflix’s stack.

Next: [Pinterest HBase Latency](pinterest-hbase-latency.md)
