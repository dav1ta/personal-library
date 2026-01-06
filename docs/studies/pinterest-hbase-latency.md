# 🔥 Pinterest – Fixing HBase Tail Latency

## 1. Background

Pinterest’s recommendation and feed systems:

- Store and retrieve large volumes of user- and pin-level signals.
- Depend on low-latency reads from storage to serve recommendations.
- Must handle heavy read/write traffic with strong consistency properties.

HBase was used as a primary data store for some recommendation workloads. As the system grew, tail latency (especially p95/p99) became a major problem.

## 2. Problem

From all.md:

- Problem: Recommendation system queries spiked to multi-second latency.
- Cause: HBase compactions + hotspotting.
- Solution:
  - Redesigned data layout using hash-distributed keys.
  - Offloaded heavy aggregations to Spark streaming.
  - Added auto-tiering between SSD and HDD.
- Impact: p99 latency stabilized under 50 ms.

Concretely, the symptoms were:

- Occasionally, recommendation queries took seconds instead of milliseconds.
- Compactions and hotspot regions in HBase caused long pauses and queueing.
- Even if average latency looked acceptable, tail latency made the system unreliable.

## 3. Root Causes

### 3.1 HBase compactions

HBase periodically compacts SSTables (HFiles):

- Major compactions rewrite large portions of data.
- During compactions, IO and CPU usage spike.
- Reads may have to touch multiple files and cope with IO contention.

At scale:

- Compactions overlap with user traffic.
- Latency spikes as disks are busy and read paths become more expensive.

### 3.2 Hotspotting on specific keys/regions

Data modeling issues:

- Sequential or skewed row keys (e.g., user IDs or time-based keys) concentrated load on a small subset of regions.
- A few hot regions handled a disproportionate amount of traffic.

Effects:

- Those region servers became overloaded.
- Even clients hitting other regions might be impacted due to shared resources (e.g., disks, network).

### 3.3 Doing heavy aggregations online

Some recommendation queries:

- Performed aggregations or joins directly on data read from HBase.
- Required scanning many rows or calculating aggregates at request time.

Combined with storage issues:

- High CPU/IO usage during online queries.
- When HBase slowed down, application-level work compounded the problem.

## 4. Architecture Before (Simplified)

A simplified view of the original path:

```text
Client request
      │
      ▼
┌────────────────────────┐
│ Recommendation Service │
│  - Online HBase reads  │
│  - Aggregations        │
└───────────┬────────────┘
            │
            ▼
        HBase Cluster
  (sequential/skewed row keys)
```

Characteristics:

- Recommendation service directly queried HBase with keys vulnerable to hotspotting.
- Some queries triggered large scans or complex logic.
- HBase compactions and region hotspots translated directly into user-visible latency spikes.

## 5. Architecture After (Optimized)

Pinterest addressed the problem through three main strategies:

1. **Hash-distributed keys** to avoid hotspots.
2. **Streaming pre-aggregation** with Spark to remove heavy work from the request path.
3. **Auto-tiering storage** to balance SSD and HDD usage.

Conceptually:

```text
          Spark Streaming Jobs
            │          ▲
            ▼          │
      Precomputed Views / Tables
            │
            ▼
┌────────────────────────┐
│ Recommendation Service │
│  - Light HBase reads   │
│  - Simple lookups      │
└───────────┬────────────┘
            │
            ▼
        HBase Cluster
   (hash-distributed row keys,
    SSD/HDD auto-tiering)
```

## 6. How They Implemented the Fix (Step by Step)

### 6.1 Redesign HBase row keys with hash distribution

To remove hotspots:

- Introduce a hash prefix into row keys.

For example, instead of:

- `row_key = user_id`

Use:

- `row_key = hash(user_id) + ":" + user_id`

This change:

- Spreads logically related rows across regions.
- Prevents a small range of keys from overwhelming a single region server.
- Requires careful design to keep scans efficient when necessary.

Migration steps:

- Backfill data into tables with new key schema.
- Update application code to use new keys.
- Gradually shift traffic from old tables to new ones.

### 6.2 Offload heavy aggregations to Spark streaming

Instead of:

- Calculating heavy aggregates at request time based on raw HBase reads,

They:

- Set up Spark streaming jobs that continuously process logs/events.
- Compute aggregates and store them in precomputed tables or materialized views.

Examples of precomputed data:

- Per-user or per-pin stats.
- Popularity scores.
- Feature vectors for ranking models.

At request time:

- Recommendation service performs lightweight lookups of precomputed results.
- HBase reads become narrower and more predictable.

Benefits:

- Removes expensive CPU work from the latency-sensitive path.
- Amortizes aggregation cost over time.
- Allows more complex logic without impacting user-facing latency.

### 6.3 Introduce auto-tiering between SSD and HDD

Storage improvements:

- Place hot data (frequently accessed rows or tables) on SSD.
- Keep colder data on HDD to save cost.

Auto-tiering logic:

- Monitor access patterns and move hot regions to SSD-backed nodes.
- Demote cold data to HDD when it cools down.

Impact:

- Hot data reads see much lower IO latency.
- Compactions and scans on SSD-backed regions are less disruptive.
- The cluster can sustain higher throughput with more predictable latency.

### 6.4 Tune HBase and compaction strategies

Alongside architectural changes, they tuned:

- Compaction policies (frequency, thresholds).
- Region sizes to balance reload costs and compaction overhead.
- Read/write path settings (e.g., block cache, memstore size).

Goal:

- Reduce the probability that compactions severely impact online reads.
- Spread compaction work more evenly over time.

## 7. Performance Results

From the original summary:

- p99 latency stabilized under 50 ms.

This means:

- Worst-case latencies (for 1% of requests) dropped from multi-second spikes to a predictable tens-of-milliseconds range.
- Compactions and hotspots no longer caused severe user-visible degradation.
- The recommendation system could scale in traffic and data volume with more confidence.

## 8. Lessons and Reusable Patterns

### 8.1 Design keys to avoid hotspotting

- For distributed key-value or wide-column stores (HBase, Bigtable, Cassandra), consider hashed or composite keys.
- Avoid sequential or skewed keys that concentrate load on a small range.

### 8.2 Move heavy computation off the request path

- Use streaming and batch processing (Spark, Flink, etc.) to precompute expensive aggregates.
- Serve precomputed results in the online path for low latency.

### 8.3 Use mixed storage tiers wisely

- SSDs for hot, latency-sensitive data.
- HDDs for colder, less frequently accessed data.
- Implement policies to move data between tiers based on access patterns.

### 8.4 Tune storage systems with workload in mind

- Compaction and region settings must be adjusted for the traffic profile.
- Monitor how storage operations affect tail latency, not just throughput.

### 8.5 Focus on tail latency, not just averages

- Multi-second outliers can dominate user experience, even if averages look fine.
- Architectural and schema changes are often required to fix tail latency, not just hardware upgrades.

These principles apply to many large-scale storage-backed systems, especially those built on distributed databases like HBase, Bigtable, or Cassandra.

Next: [Shopify Black Friday Scale](shopify-black-friday-scale.md)
