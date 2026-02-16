# Event-Driven Patterns

Missing system-design topics from `to_distribute.md` that did not have related docs.

## 1) Event-Driven Architecture
Services emit facts ("OrderPlaced", "PaymentCaptured") and other services react asynchronously.

Why teams use it:
- Loose coupling between producer and consumers.
- Easy fanout (many consumers for one event).
- Better scalability for independent workflows.

Real production shape:
- Checkout service publishes `OrderPlaced`.
- Inventory reserves stock.
- Billing charges payment.
- Notification sends email/SMS.

Tradeoffs:
- Eventual consistency.
- Harder debugging without tracing/correlation IDs.
- Schema/version management is mandatory.

## 2) Saga Pattern (Choreography vs Orchestration)
Saga coordinates multi-step distributed transactions without 2PC by using local transactions plus compensations.

Choreography:
- Services react to events directly.
- Simple to start, harder to reason about as flows grow.

Orchestration:
- A central orchestrator tells each service what to do next.
- Easier visibility/control, but adds a coordinator dependency.

Real production example (travel booking):
- Reserve flight -> reserve hotel -> charge card.
- If hotel fails, compensate: cancel flight hold and refund card authorization.

## 3) Event Sourcing (and when not to use it)
Store the sequence of domain events as the source of truth, then rebuild current state by replaying events.

Why it helps:
- Full audit trail.
- Time-travel debugging.
- Natural fit with CQRS read models.

When not to use it:
- Simple CRUD domains where history is not required.
- Teams without strong event schema/versioning discipline.
- Workloads where replay/projection complexity outweighs benefits.

Practical guidance:
- Use immutable event contracts with versioning.
- Build idempotent projectors.
- Snapshot long streams to control replay cost.

## 4) CQRS (Command Query Responsibility Segregation)
Split write and read models:
- Command side validates business rules and writes state/events.
- Query side serves optimized read views.

Good fit:
- Read-heavy systems with complex query shapes.
- Domains where write invariants differ from read projections.

Tradeoff:
- More moving parts and eventual consistency between write/read sides.

## 5) Circuit Breaker + Retries + Timeouts + Bulkheads
These patterns work together:
- Timeout: fail fast on slow dependency.
- Retry: retry transient failures with backoff/jitter.
- Circuit breaker: stop hammering an unhealthy dependency.
- Bulkhead: isolate pools so one failure does not starve all traffic.

Production rule:
- Retries only for idempotent operations or operations protected by idempotency keys.

## 6) Distributed Tracing
Tracing follows one request across services using:
- Trace ID: whole request lineage.
- Span: one unit of work (service call, DB query).
- Baggage: small context propagated downstream.
- Sampling: controls cost/volume.

Real flow:
- API gateway creates trace context.
- Services propagate context headers.
- Backend correlates logs/metrics/spans by trace ID.

## 7) CAP Theorem in Real Systems
Under network partition, you choose:
- CP: keep consistency, sacrifice availability (some requests fail/block).
- AP: keep availability, accept temporary inconsistency.

Design implication:
- Pick per domain boundary, not globally. Payments often lean CP; feeds often lean AP.

## 8) Idempotency (Exactly-Once Illusion)
Exactly-once delivery is usually an illusion in distributed systems. Practical approach:
- At-least-once delivery + deduplication by idempotency key.
- Store request key + result hash + status.
- Replays return same logical result.

Key detail:
- Scope keys by tenant/user and endpoint to avoid collisions.

## 9) Data Sharding
Sharding splits data across partitions for write/read scale.

Core concerns:
- Routing: hash-based, range-based, or directory-based.
- Rebalancing: move shards with minimal downtime.
- Hot partitions: detect skew and reshard or add adaptive routing.

Operational pattern:
- Keep shard map in a control plane and cache with fast invalidation.

## 10) API Gateway
Gateway centralizes:
- Auth/authz policy enforcement.
- Rate limits and quotas.
- Routing/versioning.
- Request/response policies.
- Observability (trace start, latency/error metrics, audit logs).

Caution:
- Avoid business logic bloat in gateway; keep domain logic in services.

Next: [System Design Patterns](design.md)
