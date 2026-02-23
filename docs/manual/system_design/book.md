# System Design Notes (Outline)

A short outline of key topics to study or reference.

## Fundamentals
- Networking basics (HTTP/TCP/UDP, DNS, load balancing)
- Caching strategies and eviction
- Databases (SQL vs NoSQL, indexing)
- Consistency models and transactions

## Data Design (Often Missed)
- Access patterns first (query shapes, latency targets, read/write ratio)
- Data model boundaries (entities, ownership, lifecycle)
- Partition keys and hot-key mitigation strategy
- Secondary indexes and their write amplification cost
- Retention, TTL, archival, and legal/compliance constraints
- Schema evolution plan (backward compatibility, rollouts, backfills)
- Data migration and dual-write/verification strategy

## Data Reliability
- Durability guarantees (WAL, replication factor, quorum rules)
- RPO/RTO targets mapped to backup + restore tests
- Idempotency and deduplication for at-least-once delivery
- Exactly-once semantics boundaries (where they are real vs simulated)
- Clock/time concerns (ordering, monotonic IDs, timezone handling)

## Scalability
- Horizontal scaling + stateless services
- Sharding and partitioning
- Read replicas and CQRS
- Queues and background workers

## Reliability
- Timeouts, retries, circuit breakers
- Failover and multi-region
- Backups and disaster recovery

## Security
- AuthN/AuthZ, least privilege
- Secrets management
- Rate limiting and abuse prevention

## Observability
- Metrics, logs, traces
- SLOs/SLAs
- Data quality monitors (freshness, completeness, drift)
- Capacity signals (p95/p99 latency, queue lag, disk growth, cardinality)

## Design Interview Flow (Short)
1. Requirements and constraints.
2. High-level architecture.
3. Data model, consistency, and durability choices.
4. Scaling and partition strategy.
5. Bottlenecks, failure modes, and operational runbooks.
6. Trade-offs, costs, and next steps.

Next: [Tips](../django/django.md)
