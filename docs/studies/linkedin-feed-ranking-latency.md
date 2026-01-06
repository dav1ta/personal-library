# 🔥 LinkedIn – Solving Feed Ranking Latency

## 1. Background

LinkedIn’s feed ranking system:

- Aggregates content from many sources (connections, companies, groups, ads).
- Scores and ranks items based on relevance models.
- Must respond quickly so users see fresh, tailored content.

To generate a ranked feed, backend services often fan out to multiple data sources and features, then aggregate results. As LinkedIn scaled, this scatter-gather pattern started to hurt tail latency.

## 2. Problem

From all.md:

- Problem: Feed ranking SLA missed due to scatter-gather across too many backends.
- Cause: Large fan-out requests → tail latency explosion.
- Solution:
  - Built Voldemort “read repair” caches near compute nodes.
  - Switched to micro-batching of ranking requests.
  - Introduced predictive caching for user segments.
- Impact: 40% reduction in datacenter traffic, big latency win.

Observed issues:

- Each feed request triggered many downstream calls to fetch features and content.
- p95 and p99 latencies increased as more backends and features were added.
- Overall datacenter traffic grew, stressing network and storage systems.
- Even if most backends were fast, a single slow dependency could delay the entire response.

## 3. Root Causes

### 3.1 Scatter-gather fan-out

A typical feed ranking request:

1. Identify candidate items for the user.
2. Fan out to multiple services to fetch features (engagement stats, social graph features, profile signals, etc.).
3. Aggregate all responses.
4. Run ranking models.
5. Return top-ranked items.

As the number of dependencies grew:

- Each request depended on many RPCs.
- Tail latency compounded: one slow call could delay the whole response.
- Retry logic and timeouts added additional complexity.

### 3.2 Repeated, similar queries across users

Many users:

- Request similar or overlapping feeds (e.g., members in the same segment).
- Need similar feature sets from the same backends.

Without shared caching:

- Backends recomputed the same features repeatedly.
- Network usage increased from repeated calls for similar data.

### 3.3 Distance between compute and data

Feature and content data:

- Might live on storage systems or services located across datacenter boundaries.
- Each call crossed network hops, adding latency and load.

The ranking service’s location relative to its dependencies significantly affected response times.

## 4. Architecture Before (Simplified)

High-level view:

```text
Client
  │
  ▼
┌───────────────────────────┐
│ Feed Ranking Service      │
│  - Candidate selection    │
│  - Fan-out to backends    │
└───────────┬───────────────┘
            │
            ▼
   Multiple Backend Services
 (features, stats, graph, etc.)
```

Characteristics:

- Many synchronous RPCs per feed request.
- Minimal caching near the ranking compute.
- Heavy, repeated traffic across the network to backends.

## 5. Architecture After (Optimized)

LinkedIn introduced:

1. **Voldemort “read repair” caches** colocated near the ranking compute.
2. **Micro-batching of ranking requests** to share work and reduce duplicate calls.
3. **Predictive caching** based on user segments and usage patterns.

Conceptual view:

```text
Client
  │
  ▼
┌──────────────────────────────────────────┐
│ Feed Ranking Service                    │
│  - Candidate selection                  │
│  - Micro-batched feature fetches        │
│  - Uses local Voldemort caches          │
└───────────┬─────────────────────────────┘
            │
            ▼
     Backend Services
       (hit less often,
        mostly cache misses)
```

## 6. How They Implemented the Fix (Step by Step)

### 6.1 Deploy Voldemort caches near compute nodes

LinkedIn’s key-value store, Voldemort, was used as a read-optimized cache:

- Caches frequently used features and content close to the ranking services.
- Serves as a local, fast lookup to avoid hitting primary backends for every request.

“Read repair” behavior:

- If a cache entry is missing or stale, the ranking service fetches data from the origin backend.
- On success, it updates the Voldemort cache.
- Subsequent requests hit the cache instead of the backend.

Benefits:

- Reduces latency for repeated feature lookups.
- Lowers load and traffic to the origin services.
- Local caches can be sharded and replicated for scale and reliability.

### 6.2 Micro-batch ranking requests

Rather than handling every feed request in isolation:

- The system groups similar feature requests together in short windows.
- Requests for the same feature or key across users are combined.

Example:

- Multiple users requesting feeds within a short time window might need the same engagement features for a popular post.
- Instead of making N calls, the system makes one backend call and shares the result.

Implementation concepts:

- A small micro-batch window (e.g., a few milliseconds) where incoming feature requests are aggregated.
- A batching layer that deduplicates keys and fans them out as a single bulk query.
- Response fan-out: the bulk result is split and delivered to each request context.

Impact:

- Fewer RPCs and network calls.
- Better utilization of backend services.
- Reduced per-request overhead.

### 6.3 Predictive caching for user segments

To go further:

- Analyze user behavior and segments to predict which features/content will be needed soon.
- Pre-warm caches for these segments before they hit the system.

Examples:

- Users in a certain industry or region often see similar content.
- During specific events or campaigns, certain features or posts are likely to be requested.

Predictive caching:

- Pre-populates Voldemort caches with data relevant to those segments.
- Reduces cold misses at peak times.
- Smooths traffic patterns to backend services.

### 6.4 Reduce fan-out and tighten SLAs

With caches and micro-batching in place:

- The ranking service can set stricter timeouts for backend calls.
- If a backend is slow, cached data or partial features can still be used.
- Fewer calls go all the way to origin, reducing the scatter-gather surface area.

This constrains tail latency, even when some backends are degraded.

## 7. Performance Results

From the original summary:

- 40% reduction in datacenter traffic, with a big latency win.

Practically, this implies:

- Significantly fewer cross-service and cross-datacenter calls for feed ranking.
- Lower average and tail latencies due to cache hits and shared work.
- Better resilience to slow or overloaded backends.

## 8. Lessons and Reusable Patterns

### 8.1 Collapse repeated work with caching near compute

- Place caches as close to the compute node as possible.
- Use read-through and read-repair patterns to keep caches up to date.
- Favor hot, frequently accessed data for caching.

### 8.2 Use micro-batching to reduce fan-out

- Aggregate similar requests over short windows.
- Deduplicate keys so backends see fewer, larger requests instead of many small ones.

### 8.3 Predictive and segment-based caching

- Use historical data and segmentation to anticipate future cache needs.
- Pre-warm caches to handle spikes and common patterns.

### 8.4 Control fan-out in the hot path

- Avoid unbounded scatter-gather to many backends per request.
- Provide fallbacks (cached or partial data) when some dependencies are slow.

### 8.5 Optimize for tail latency and traffic, not just averages

- Tail latency often comes from fan-out and cross-service dependencies.
- Reducing redundant traffic yields both performance and cost benefits.

These techniques generalize to any large-scale, personalized feed or search system with many backend dependencies.

Next: [Netflix GC Pauses](netflix-gc-pauses.md)
