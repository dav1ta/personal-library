# Future Enhancements — job-queue-service

Purpose: capture small, high‑leverage next steps. Keep it one file where possible, prefer stdlib, and layer changes incrementally.

## P0 — Near‑Term Improvements
(Status: implemented in src/fastapi_app.py)
- Multi‑pod Idempotency (shared store)
  - Why: current in‑memory dedupe works per pod only.
  - How: use SQLite (stdlib) with unique key + expires_at for atomic set-if-not-exists; optional Redis/Postgres later.
  - Accept: duplicate requests across pods return prior status; no double enqueue within TTL.
  - Implemented: optional `IDEMP_BACKEND=sqlite` + `IDEMP_SQLITE_PATH` envs; functions `idem_get_status`, `idem_set_accepted`, `idem_set_completed`; TTL sweeper deletes expired rows.
- TTL Sweeper for Idempotency
  - Why: bound memory; remove expired keys.
  - How: background task every 30s purges keys with expires_at <= now.
  - Accept: idempotency map size stays stable under load.
  - Implemented: background task `_idem_sweeper()` for memory and sqlite.
- /metrics (Prometheus text)
  - Why: better observability than ad‑hoc JSON.
  - How: expose counters/gauges (processed, failures, queue_depth, dlq, rate tokens).
  - Accept: curl /metrics shows valid exposition format.
  - Implemented: `GET /metrics` returns exposition (`job_*`, `rate_*`).
- Per‑process Safe Rate Limiter
  - Why: current token bucket is global in one process only.
  - How: note in README; optionally shard by client id (header) to improve fairness.
  - Accept: basic per‑client tokens, no global starvation.
  - Implemented: per-client token buckets keyed by `X-Client-Id` (fallback to remote IP).

## P1 — Scheduling & Reliability
- Visibility Timeout + Ack/Requeue
  - Why: recover jobs when workers crash/hang; avoid losing in‑flight work.
  - How: track in_flight[job_id]=deadline; background sweeper requeues overdue.
  - Accept: jobs return to queue if not completed by T; no duplication when completed.
- Delayed Jobs + Priority + Fairness
  - Why: more realistic scheduling; avoid starvation.
  - How: heapq scheduler on (due_at, -priority, seq); mover task into ready queue; round‑robin per tenant.
  - Accept: run_at respected within tolerance; higher priority drains first without starving others.
- Job Status API
  - Why: clients need visibility.
  - How: in‑memory map job_id -> status/attempts/result.
  - Accept: GET /jobs/{id} returns terminal state and attempts.

## P2 — Durability & Exactly‑Once Effects
- Persistence (SQLite) for Queue and DLQ
  - Why: survive restarts; enable multi‑process safety.
  - How: tables (jobs, inflight, dlq); load at startup; flush on change.
  - Accept: graceful restart does not lose accepted jobs.
- Outbox Pattern for Side Effects
  - Why: at‑least‑once engine, exactly‑once effects.
  - How: write outbox record before effect; dedupe by idempotency key; deliverer drains outbox.
  - Accept: replays do not double‑apply effects.

## Observability & Ops
- Tracing (OpenTelemetry, optional)
  - Correlate enqueue → processing spans via request/job ids.
- Error Budgets & SLOs
  - Define p99 enqueue latency, success rate, DLQ growth ceilings.
- Structured JSON logs
  - Include job_id, attempt, latency_ms, error_type.

## Security & Limits (as needed)
- Input validation & size limits; auth/tenancy via headers.
- Quotas per tenant; reject oversize payloads early.


## Code Pointers (current)
- FastAPI app: src/fastapi_app.py
  - Idempotency store/check/update: lines ~55, ~233‑241, ~259‑265, ~131‑139
  - Backpressure (bounded queue + 429): queue init + enqueue check
  - Backoff + Jitter: process_job() → delayed_requeue()
  - DLQ endpoints: /dlq, /dlq/requeue
  - Rate limiter: rl_consume() in /enqueue

## Test Ideas
- Flood with duplicate keys across concurrent requests; assert 1 accepted.
- Kill a worker mid‑processing; assert visibility timeout requeues.
- Burst beyond Q_MAX; assert 429 rate and stable memory.
- Force failures > MAX_ATTEMPTS; assert DLQ growth and requeue works.

## Spin‑Off Project Ideas (to explore next)
- caching-strategies: TTL+LRU/LFU, cache‑aside vs write‑through, negative caching, bloom filters, jitter.
- search-inverted-index: shard/replicate, eventual consistency, query routing, top‑K scoring.
- feature-flags-service: read‑heavy, streaming updates, consistency levels, rollout policies.
- metrics-gateway: scrape/remote‑write, relabeling, cardinality control, exemplars.
- protocol-lab: HTTP/gRPC/WebSocket/SSE comparisons, backpressure semantics.

If you want, I can wire P0 items now (TTL sweeper, /metrics, and SQLite idempotency) while keeping it one file.
