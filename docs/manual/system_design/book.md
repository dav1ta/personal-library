## Chapter 1: System Design Patterns

## Back-of-Envelope Math (Fast)
- Round-number estimates.
- `Peak QPS = avg QPS x burst factor`.
- `Concurrency ~= QPS x latency_seconds`.
- `Daily storage ~= events/day x avg bytes x replicas`.
- `Queue drain time ~= backlog / drain_rate`.
- `Cache memory ~= hot_keys x object_size x overhead`.
- Example: `20k` QPS at `80ms` -> `~1600` in flight.
- Example: `3M` backlog at `30k/min` -> `~100 min` drain.

## 1) Clarify Requirements
- Users?
- Peak QPS?
- Latency target?
- Data size and growth?
- Read-heavy or write-heavy?
- Strong or eventual consistency?
- Uptime/SLO target?
- Example: feed read `<200ms`; uploads can be slower.

## 2) Shape the Data Flow
- Request path.
- Split read/write paths.
- Mark hot paths first.
- Mark async/background work.
- Example: read `client -> CDN -> API -> cache -> DB`; write `client -> API -> DB -> queue -> worker`.

## 3) Core Building Blocks
- LB for traffic spread.
- Autoscale stateless services.
- Cache to cut DB reads.
- Queues for async jobs.
- Choose DB by access pattern.
- Example: thumbnails -> worker queue; profile reads -> Redis cache.

## 4) Scaling Patterns
- Scale stateless APIs horizontally first.
- Partition when one DB gets hot.
- Read replicas for read-heavy traffic.
- Keep the write path simple early.
- Example: feed reads -> replicas; writes -> primary.

## 5) Consistency and Transactions
- Strong consistency for money/identity; eventual for derived views (feeds/counters).
- Idempotency keys for retryable writes.
- Version APIs/events/schemas.
- Schema changes: expand/contract; keep dual-read during rollout.
- Keep transactions small.

## 6) Reliability
- Timeouts on every network hop.
- Retry only idempotent ops; cap retries and add jitter.
- Circuit breakers stop retry storms.
- Bulkheads isolate failures.
- Graceful degradation path (e.g., recent posts if recommendations fail).

## 7) Observability
- Logs + metrics + traces; add request/correlation IDs.
- Track latency, errors, throughput, saturation.
- By layer: API `p95/p99`, DB slow queries/replica lag, cache hit rate, queue depth/lag.
- Alert on user impact + likely cause pairs (`/feed p95` + cache hit drop; queue lag + worker errors).

## 8) Security
- Separate AuthN (who) and AuthZ (what).
- Least privilege for services.
- Rate limits + abuse controls.
- Store/rotate secrets safely.
- Example: upload API = token + per-user rate limit.

## 9) Deployment / Ops
- Canary or blue/green deploys; feature flags for risky changes.
- Keep schema rollouts backward compatible (expand/contract).
- Predefine rollback steps.
- Back up data and test restores.
- Keep a short incident runbook.
- Example: 5% canary -> watch errors/latency -> expand.
- Example: add column -> deploy readers -> backfill -> switch writers -> clean old path later.

## Worked Example (Feed v1)
- Goal: photo feed v1, read-heavy, `/feed p95 <200ms`.
- Consistency split: profile edits strong; counters eventual (`2-3s` lag okay).
- Read path: client -> `CDN` -> API -> cache -> replica.
- Write path: client -> API -> primary DB -> queue -> worker (`S3` for media).
- Core blocks: `AWS ALB`, stateless API, `Redis`, `PostgreSQL`, queue, worker, `S3`.
- Scale first: add API nodes; shard cache by user hash if hot.
- Safety defaults: idempotent uploads/jobs; timeouts + capped retries/jitter; degrade recommendations.
- Observe + ops: `/feed p95/p99`, cache hit rate, queue lag, worker errors; canary `5%`; rollback on error + latency + saturation.
- Failure: new post not visible due to replica lag (read-after-write miss).
- Fix: sticky reads to primary for a short post-write window.
- Rollout: add nullable column -> dual-read -> backfill -> switch writes.

## Fast Defaults and Traps
| Situation | Fast default | Trap |
| --- | --- | --- |
| Hot read-heavy endpoint | `CDN` + `Redis` (+ replica if needed) | Stampede/invalidation; replica lag breaks read-after-write |
| Simple async jobs | `SQS`/`RabbitMQ` + worker + DLQ | Uncapped retries; queue growth; no replay runbook |
| High-fanout events / replay | `Kafka` + consumer groups | Hot partitions; consumer lag; schema drift |
| OLTP business records | `PostgreSQL`/`MySQL` | Hot write paths/keys; ignoring `p99` |
| Search from primary DB data | `OpenSearch` + CDC/queue indexing | Read-your-write gaps; reindex cost |
| Analytics scans/aggregations | `ClickHouse`/`BigQuery` | Using analytics store as OLTP |
| File upload/media pipeline | pre-signed `S3`/`MinIO` + queue + worker | API bottleneck; scan/thumbnail backlog |
| Checkout / payment flow | OLTP + idempotency keys + outbox/queue/saga | Duplicate side effects; weak compensation boundaries |
| Login/public API protection | Gateway (`Envoy`) + rate limits + audit logs | Bot spikes; bad lockouts; weak abuse controls |
| Internal service calls | `REST` (compatibility), `gRPC` (low-latency/streaming) | Version drift (`REST`); client/tool fit (`gRPC`) |
| Observability baseline | `OpenTelemetry` + `Prometheus` + `Grafana` + logs/traces | Missing correlation IDs; infra-only alerts |
| Deploy + schema rollout | Canary/blue-green + flags + expand/contract | One-metric canaries; schema-incompatible rollback |
| Time-sensitive auth/events | Server timestamps + NTP | Clock skew breaks expiry/order assumptions |

---

## Chapter 2: Event-Driven Patterns

Definition-first fit/risk notes for async systems.

## 1) Event-Driven Architecture
Publish events; consumers react asynchronously.
- Fit: Async fanout, loose coupling, add consumers without changing producers.
- Risk: Duplicates, ordering gaps, schema drift, weak tracing without event/correlation IDs.
- Example: `OrderPlaced` triggers inventory, billing, notifications.
- Not fit: Single-service CRUD with strict synchronous consistency.

## 2) Saga Pattern (Choreography vs Orchestration)
One large transaction becomes local async transactions plus compensation.
- Fit: Cross-service workflow without 2PC; choreography for small/simple flows, orchestration for visibility/sequence control.
- Risk: Partial completion if compensation logic is incomplete or non-idempotent.
- Example: Travel booking reserves flight/hotel, charges card; failed step triggers compensation.
- Not fit: One DB transaction solves it, or steps cannot be compensated safely.

## 3) Event Sourcing (and when not to use it)
Store immutable events as the source of truth; rebuild state from the stream.
- Fit: Audit/history/replay matters; temporal debugging and domain history are core needs.
- DB: Read state is projection-derived (often with snapshots for long streams).
- Risk: Projection lag, replay cost, versioning mistakes, higher operational complexity.
- Example: Account state from `Created`, `Debited`, `Credited`, `Closed`.
- Not fit: CRUD apps that only need current state.

## 4) CQRS (Command Query Responsibility Segregation)
Separate command (write) and query (read) models.
- Fit: Read-heavy systems or query shapes that need denormalized views while writes need strict rules.
- DB: Read projections/stores can differ from the write model; expect read lag.
- Risk: Stale reads, projection drift, dual-model complexity.
- Example: Catalog writes to OLTP model; search/list pages read a denormalized projection.
- Not fit: One model serves reads and writes well.

## 5) Circuit Breaker (with Retries and Timeouts)
Fail fast on unhealthy dependencies with timeouts, retries, and breaker state.
- Fit: Remote calls where latency/failure can cascade; use short timeouts and capped backoff+jitter retries.
- Risk: Retry storms/cascading failure from long timeouts, uncapped retries, or retrying unsafe side effects.
- Example: Recommendations stalls; API times out, opens breaker, serves fallback.
- Not fit: In-process calls or non-idempotent side effects without protection.

## 6) Bulkhead (Isolation Boundaries)
Isolate capacity so one workload or dependency cannot starve a critical path.
- Fit: Mixed critical and best-effort traffic; isolate worker pools, queues, DB/connection pools, and breaker state; reserve capacity for critical paths.
- Risk: Shared global pools let one backlog or dependency cause a platform-wide outage.
- Example: Email/webhook backlog fills workers and slows checkout requests.
- Not fit: Very small systems with one dependency and no real contention.

## 7) Outbox + CDC (Reliable Event Publish)
Make a DB write and follow-up event publish reliable without 2PC.
- DB: Write the business row and publish intent in one local transaction (outbox), or stream durable changes from binlog/WAL (CDC).
- Fit: Reliable event publish without 2PC; preserve ordering per aggregate when needed and dedupe by event ID/version.
- Risk: "Write succeeded, event lost" gaps, duplicate publishes, relay backlog, or ordering mistakes if relay/consumer design is weak.
- Example: Order row commits but `OrderPlaced` is missing unless publish intent is stored durably.
- Not fit: Best-effort notifications can be lost, or no durable transaction boundary exists.

## 8) Idempotency (Exactly-Once Illusion)
Return the same logical result when retries or replays repeat a write.
- DB: Use durable dedupe state keyed by tenant/user + endpoint; keep TTL longer than retry/redelivery windows.
- Fit: Practical exactly-once substitute when clients retry, queues redeliver, or timeouts hide the first result; store key + status/result and return the same logical result on replay.
- Risk: Duplicate side effects or false dedupe from bad key scope/TTL.
- Example: Payment times out, client retries with the same key, server returns the original result.
- Not fit: Pure reads or handlers that cannot return a stable replay result.

## 9) Rate Limiting (Protect Dependencies)
Cap request rate to protect dependencies and keep usage fair.
- Fit: Shared APIs/dependencies under burst traffic, abuse, or unfair tenant usage; use edge/service limits (token bucket or window-based) and return `429` + `Retry-After` when possible.
- Risk: Bad thresholds block healthy users; weak limits fail to protect dependencies.
- Example: Login and public APIs need protection during bot spikes.
- Not fit: Low-volume trusted systems where limits add noise with little protection.

## 10) API Gateway (Edge Auth, Routing, Throttling)
One edge entry point for auth, routing, throttling, and cross-cutting policy.
- Fit: Many clients hit many services; centralize auth/authz, routing/versioning, quotas/rate limits, tracing, and cross-cutting policies while keeping domain logic in services.
- Risk: The gateway becomes a bottleneck or a business-logic dumping ground.
- Example: Web/mobile traffic enters a gateway for auth, routing, quotas, and request tracing.
- Not fit: One small service where direct access is simpler.

See also: [System Design Patterns](#chapter-1-system-design-patterns)

---

## Chapter 3: System Design Good Practices

Production anti-patterns and resilience defaults.

## Retry Safety: Unbounded Retries (Anti-Pattern)

### What it is
- Retries without hard limits on attempts, total time, or concurrency.
- Often stacked across layers (client, gateway, service, queue redelivery).

### When to use
- Rarely.
- Only for short manual recovery scripts with strict scope, rate limits, and supervision.

### When not to use
- Any user-facing request path with latency expectations.
- Calls to overloaded or flaky dependencies.
- Non-idempotent operations (payments, emails, external effects) without safeguards.

### Failure modes
- Retry storms amplify outages.
- Backlogs and worker saturation raise tail latency.
- Missing idempotency creates duplicate side effects.
- Cascading failure as shared pools are consumed by retries.

### Metrics to watch
| Metric | Signal |
| --- | --- |
| Retry rate; attempts per request/job | Amplification risk |
| Success-after-retry ratio | Useful vs wasted retries |
| Downstream `5xx` / timeout rate | Dependency health |
| Queue depth, worker saturation, lag | Backlog pressure |
| `429` / `503`; circuit-breaker opens | Overload / protection events |

### Common mistakes
- No max attempts or no total deadline.
- Fixed-delay retries without jitter.
- Retrying all errors (including validation/auth).
- Nested retries at multiple layers.
- Retrying after caller deadline expires.
- Retrying non-idempotent actions without idempotency keys.

### Small real example
Order service calls payment provider:

| Case | Policy |
| --- | --- |
| Bad | Retry forever on timeout in request handler |
| Better | Per-attempt timeout, max 2-3 retries, exponential backoff + jitter, total deadline, idempotency key, circuit breaker |

Minimal policy:
- Retry transient errors only.
- Bound attempts and total time.
- Add jitter.
- Propagate deadlines.
- Emit retry metrics.

## Related Good Practices (Retry/Resilience)

- Bounded retries with exponential backoff + jitter.
- Timeouts before retries.
- Idempotency keys for retryable side effects.
- Circuit breakers + concurrency limits to prevent overload amplification.
- Retry budgets to cap retry traffic.

---

## Chapter 4: Common System Design Problems

## Availability and Reliability
| Problem | Patterns |
| --- | --- |
| Spiky traffic | Autoscaling, queues, load shedding |
| Hot dependency | Timeouts, bounded retries, circuit breakers, caching |
| Single region risk | Multi-region failover, backups, DNS routing |

## Data and Storage
| Problem | Patterns |
| --- | --- |
| Write scaling | Partitioning/sharding, async writes |
| Read scaling | Caching, replicas, denormalized views |
| Large files | Object storage + CDN |

## Consistency
| Problem | Patterns |
| --- | --- |
| Strong vs eventual consistency | Choose per domain; use sagas for workflows |
| Concurrent updates | Optimistic locking, idempotency keys |

## Real-Time
| Problem | Patterns |
| --- | --- |
| Notifications/streams | WebSockets, SSE, pub/sub |
| Fanout | Queue + workers; avoid N^2 broadcast |

## Security
| Problem | Patterns |
| --- | --- |
| Auth at scale | Centralized identity, token validation, scopes |
| Abuse | Rate limiting, WAF, bot detection |

## Operations
| Problem | Patterns |
| --- | --- |
| Logs/metrics | Centralized logs, tracing, SLOs |
| Schema changes | Expand/contract migrations, backfills |

## Architecture Choices
| Problem | Patterns |
| --- | --- |
| Monolith vs microservices | Start modular; split when needed |
| Sync vs async | Async for slow or retryable work |

---

## Chapter 5: Distributed Systems & Data Glossary

Fast lookup for distributed-systems and database terms.

## Replication and Consistency
| Term | Def | Note |
| --- | --- | --- |
| Read replicas | Read-only copies of a primary for read scaling. | Watch lag and staleness. |
| Leader-Follower replication | One leader accepts writes and replicates to followers. | Failover and lag dominate ops. |
| Multi-leader replication | Multiple nodes accept writes and replicate among peers. | Conflict resolution is the hard part. |
| Logical replication | Replicates logical row/table/event changes, not storage blocks. | Useful for selective sync, CDC, upgrades. |
| Physical replication | Replicates storage pages or WAL streams. | Fast for full replicas and failover, less flexible. |
| Geo-replication | Replication across regions for DR, latency, or isolation. | Trades latency, consistency, conflicts, and cost. |
| Quorum | A read/write needs a minimum replica count. | Defined by `R`/`W` choices. |
| Consensus | Protocols let nodes agree despite failures. | Used for election and coordination. |
| CAP theorem | With a partition, choose consistency or availability. | Partition tolerance is assumed. |
| BASE | Basically Available, Soft state, Eventual consistency. | Availability-first model. |
| ACID | Atomicity, Consistency, Isolation, Durability. | Transaction goal inside one DB boundary. |
| Eventual consistency | Reads can be stale but converge later. | Fits feeds, analytics, async work. |
| Strong consistency | Reads return the latest committed write by the model. | Needed for strict invariants. |
| Snapshot isolation | Transactions read a stable snapshot and avoid many conflicts. | Write skew can still occur. |
| MVCC (Multi-Version Concurrency Control) | Stores row versions so reads and writes block less. | Basis for snapshot isolation. |
| Two-phase commit (2PC) | Distributed commit with prepare and commit phases. | Atomic but slow and fragile. |
| Three-phase commit (3PC) | 2PC variant with an added phase to reduce blocking. | Rare; sagas or consensus are often preferred. |
| Exactly-once semantics | An approximation built from retries, dedupe, and idempotency. | Build on at-least-once plus idempotent handlers. |
| Idempotency | Repeats produce the same logical result for a request key. | Essential for retries and redelivery. |
| CDC (Change Data Capture) | Streams DB changes to downstream systems. | Often paired with an outbox. |

## Storage Internals and Recovery
| Term | Def | Note |
| --- | --- | --- |
| Write-ahead logging (WAL) | Persist changes to a log before data pages. | Enables crash recovery and replication. |
| Checkpointing | Periodically persist a durable state snapshot. | Cuts crash replay work. |
| Compaction | Rewrite/merge on-disk data to reclaim space and drop old versions. | Core to LSM and log-structured systems. |

## Partitioning, Sharding, and Placement
| Term | Def | Note |
| --- | --- | --- |
| Clustering | Multiple nodes operate as one system. | Define role and failure model. |
| Rebalancing | Move data/traffic to even out load and storage. | Triggered by topology or load changes. |
| Resharding | Change shard boundaries/count and move data. | Costly; plan online moves and backpressure. |
| Data locality | Keep compute near the data it uses. | Cuts latency and network cost. |
| Hot partition | One partition gets disproportionate traffic or data. | Fix keys, spread writes, or cache. |
| Split-brain | Two parts of a system both believe they are leader. | Prevent with quorum, election, fencing. |
| Hash partitioning | Hash a key to place data across partitions. | Even spread, weak for range scans. |
| Range partitioning | Partition by key ranges. | Great for range scans, can hot-spot. |
| Time-series partitioning | Partition by time windows. | Helps retention and query pruning. |
| Consistent hashing | Hashing that minimizes key moves when nodes change. | Used in caches and distributed KV. |

## Scaling and Reliability
| Term | Def | Note |
| --- | --- | --- |
| Failover | Switch to a standby when the active node/service fails. | Needs correct detection and client retries. |
| High availability (HA) | Keep service running through failures via redundancy and isolation. | Built with failover and fault isolation. |
| Horizontal scaling | Add nodes/instances to increase capacity. | Best fit for stateless and distributed systems. |
| Vertical scaling | Add CPU/RAM/storage to one node. | Simple, but capped and expensive. |
| Load balancing | Distribute traffic across instances. | Uses routing, health checks, and stickiness. |
| Connection pooling | Reuse DB/network connections across requests. | Pool size must match backend limits. |
| Caching | Store hot data closer to callers. | Define TTL, invalidation, and staleness. |
| Backpressure | Signal producers/callers to slow down under overload. | Prevents queue growth and cascades. |
| Circuit breaker | Fail fast on unhealthy dependencies, then probe recovery. | Reduces blast radius. |
| Throttling | Slow or cap processing rate to protect capacity. | Can be per client, endpoint, or system. |
| Rate limiting | Enforce quotas over time windows. | Supports fairness and abuse control. |

## Data Modeling and Query Performance
| Term | Def | Note |
| --- | --- | --- |
| Normalization | Structure relational data to reduce duplication and improve integrity. | Better correctness, more joins. |
| Denormalization | Duplicate data or precompute views to speed reads. | Faster reads, more sync/invalidation work. |
| Indexing | Extra structures that speed lookups, filters, joins, and sorts. | Faster reads, higher write/storage cost. |
| Secondary indexes | Indexes on non-primary-key columns. | Enable alternate access paths. |
| Composite index | Index on multiple columns. | Column order matters. |
| Covering index | Index includes all columns a query needs. | Avoids base-row fetches. |
| Materialized views | Stored query results refreshed periodically or incrementally. | Useful for heavy aggregates. |
| Bloom filter | Probabilistic membership check with false positives only. | Avoids costly misses. |
| LSM tree | Write-optimized storage with memtables, sorted runs, and compaction. | Compaction-heavy design. |
| B-tree | Balanced tree for efficient point and range lookups. | Common default index structure. |
| Query planner | DB component that picks a SQL execution plan. | Bad plans cause latency spikes. |
| Cost-based optimizer | Planner that estimates candidate plan cost. | Good statistics are required. |

## Concurrency Control and Anomalies
| Term | Def | Note |
| --- | --- | --- |
| Deadlock | Transactions wait on each other indefinitely. | DB aborts one; app retries safely. |
| Lock escalation | DB replaces many fine-grained locks with a coarser lock. | Lowers lock overhead, lowers concurrency. |
| Optimistic locking | Assume low conflict and detect collisions at write/commit. | Version checks suit low-conflict workloads. |
| Pessimistic locking | Lock rows/resources before updates. | Safer for conflict-heavy writes, lower throughput. |
| Dirty read | Read data from an uncommitted transaction. | Rollback can invalidate the read. |
| Phantom read | Re-running a query returns a different row set. | Concurrent inserts/deletes changed the match set. |
| Read skew | Transaction reads related values from different times. | Produces an inconsistent snapshot view. |
| Write skew | Concurrent writes each look valid but break an invariant together. | Snapshot isolation can allow this. |
| Data skew | Uneven data distribution across partitions/nodes. | Causes imbalance and slow work. |

## Data Platforms and Schema Management
| Term | Def | Note |
| --- | --- | --- |
| Federation | Query/serve across independent systems without full centralization. | Preserves autonomy; hurts joins, latency, reliability. |
| Data lake | Raw or semi-structured analytics storage, often object storage. | Flexible ingest; governance must be added. |
| Data warehouse | Curated analytics store for SQL reporting and BI. | Stronger schema and performance expectations. |
| Columnar storage | Stores data by column. | Better compression and scans for OLAP. |
| Row-based storage | Stores full rows together. | Better for OLTP point reads/writes. |
| Data migration | Move or transform data across schemas, systems, or regions. | Plan validation, backfill, cutover, rollback. |
| Schema evolution | Change schemas over time while keeping compatibility. | Prefer backward-compatible staged rollouts. |
| Schema registry | Service for versioned schemas and compatibility rules. | Key for shared event contracts. |

## See Also

- [Chapter 2: Event-Driven Patterns](#chapter-2-event-driven-patterns)
- [Chapter 1: System Design Patterns](#chapter-1-system-design-patterns)
