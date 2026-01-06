# System Design Patterns (Concise)

Use this as a practical checklist when designing services.

## 1) Clarify Requirements
- Users, traffic, latency, data size, read/write ratio.
- Consistency needs (strong vs eventual).
- Availability targets (SLO/SLA).

## 2) Shape the Data Flow
- Request path (client -> edge -> API -> data).
- Read path vs write path (different optimizations).
- Hot paths vs cold paths.

## 3) Core Building Blocks
- Load balancing + autoscaling.
- Caching (client, CDN, edge, app, DB).
- Queues for async work.
- Databases (SQL/NoSQL) chosen by access pattern.

## 4) Scaling Patterns
- Horizontal scaling for stateless services.
- Sharding/partitioning for large datasets.
- Read replicas for read-heavy workloads.

## 5) Consistency and Transactions
- Strong consistency for money/identity.
- Eventual consistency for feeds, analytics.
- Use idempotency keys for retries.

## 6) Reliability
- Timeouts, retries, circuit breakers.
- Bulkheads to isolate failures.
- Graceful degradation.

## 7) Observability
- Logs + metrics + traces.
- SLOs and alerting on user-facing symptoms.

## 8) Security
- AuthN/AuthZ, least privilege.
- Rate limits and abuse controls.
- Secrets management and rotation.

## 9) Deployment / Ops
- Blue/green or canary releases.
- Rollback strategy.
- Backups + restore tests.

## Typical Architecture Template
- API layer -> services -> data store
- Cache in front of data store
- Queue + worker for background jobs
- Metrics/logs everywhere
