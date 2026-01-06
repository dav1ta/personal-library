# Common System Design Problems (Checklist)

Each item includes the usual go-to patterns.

## Availability and Reliability
- **Spiky traffic:** autoscaling, queues, load shedding.
- **Hot dependency:** timeouts, retries, circuit breakers, caching.
- **Single region risk:** multi-region failover, backups, DNS routing.

## Data and Storage
- **Write scaling:** sharding/partitioning, async writes.
- **Read scaling:** caching, read replicas, denormalized views.
- **Large files:** object storage + CDN.

## Consistency
- **Strong vs eventual:** choose per domain; use sagas for workflows.
- **Concurrent updates:** optimistic locking, idempotency keys.

## Real-Time
- **Notifications/streams:** WebSockets, SSE, pub/sub.
- **Fanout:** queue + workers; avoid N^2 broadcasts.

## Security
- **Auth at scale:** centralized identity, token validation, scopes.
- **Abuse:** rate limiting, WAF, bot detection.

## Operations
- **Logs/metrics:** centralized logging + tracing + SLOs.
- **Schema changes:** expand/contract migrations, background backfills.

## Architecture Choices
- **Monolith vs microservices:** start modular; split only when needed.
- **Sync vs async:** use async for slow or retryable work.
