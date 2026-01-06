# job-queue-service — Problems & Solutions (Refactored)

> Each entry follows the same pattern: problem with a concrete example, then the solution, relevant theorems/patterns, and good practices.

---

Concurrency — Bounded Worker Pool
- Problem (example): Under bursty load, unbounded threads/tasks thrash CPU/IO and collapse latency (e.g., 10k jobs in 5s causes timeouts and context-switch storms).
- Solution / Theorem / Good practice: Use a fixed worker pool with a bounded queue to control WIP; apply backpressure (HTTP 429) when full; cancel/timeout slow work; think via Little’s Law (L = λ·W) to keep WIP bounded.

Retries + Dead Letter Queue (DLQ)
- Problem (example): Transient failures (timeouts, 5xx) trigger lockstep retries and retry storms; permanent failures loop forever and block progress.
- Solution / Theorem / Good practice: Exponential backoff with jitter, cap attempts, then send to a DLQ; optional circuit breaker for persistent upstream failures; jitter avoids synchronized retry spikes; record failure reasons for inspection/requeue.

Idempotency for At-least-once Processing
- Problem (example): Crashes and visibility timeouts can re-run jobs, causing duplicate side effects (e.g., duplicate POST to an external API).
- Solution / Theorem / Good practice: Use idempotency keys (client-provided or derived) with TTL to dedupe; return previous result on duplicate; accept at-least-once delivery and design effects to be replay-safe when possible.

Backpressure + Rate Limiting on Enqueue
- Problem (example): Producers can enqueue faster than workers drain, leading to unbounded memory and rising latency (e.g., flood at 20k rps).
- Solution / Theorem / Good practice: Enforce a bounded queue and return 429 when full; add a token-bucket rate limiter (global or per-tenant); expose queue depth; prefer shedding to protect p99s; optionally include Retry-After.

Observability: Metrics, Logs, Tracing
- Problem (example): Can’t explain throughput drops or latency tails; no visibility into queue depth, retries, or DLQ growth.
- Solution / Theorem / Good practice: Track counters/gauges (enqueued, processed, failures, queue_depth, workers), structured logs with correlation IDs, and expose a `/stats` (and later `/metrics`) endpoint; follow RED/USE principles for what to measure.

Performance: Batch vs Single-item Processing
- Problem (example): Per-item overhead (syscalls/RTTs) dominates even when CPU is idle; throughput flatlines at low utilization.
- Solution / Theorem / Good practice: Allow small batches to amortize per-call overhead; size batches conservatively to avoid head-of-line blocking; prefer vectorized I/O when available.

Caching: Result Cache + Invalidation
- Problem (example): Repeat inputs drive unnecessary recomputation and higher contention (e.g., 30% duplicate payloads within 10 minutes).
- Solution / Theorem / Good practice: Cache-aside with in-memory LRU and TTL (with jitter) keyed by idempotency key or payload hash; invalidate on writes and keep cache size bounded.

Consistency: CAP Trade-offs in a Queue
- Problem (example): With persistence/replication, partitions force trade-offs: do we accept jobs (availability) or reject to keep strict ordering/consistency?
- Solution / Theorem / Good practice: Prefer AP (availability) with eventual consistency and idempotency-based convergence for general job intake; document semantics; consider a strict mode for sensitive workflows; understand CAP and its implications for enqueue/dequeue.

---

Code References (FastAPI app)
- File: `job-queue-service/src/fastapi_app.py`

Idempotency (dedupe) — Where we save IDs and how it works
- In-memory store (single process):
```
# Global store (guarded by async lock)
idempotency: Dict[str, Dict[str, Any]] = {}
```
- Enqueue check (short‑circuit duplicates):
```
idem_key = request.headers.get("X-Idempotency-Key") or request.headers.get("Idempotency-Key")
if idem_key:
    async with lock:
        record = idempotency.get(idem_key)
        if record and record.get("expires_at", 0) > now():
            return {"status": record["status"], "idempotent": True}
```
- Save "accepted" on first enqueue:
```
if idem_key:
    async with lock:
        idempotency[idem_key] = {
            "status": "accepted",
            "result": None,
            "expires_at": now() + IDEMP_TTL,
        }
```
- Mark completed in worker when done:
```
key = item.get("idempotency_key")
if key:
    async with lock:
        record = idempotency.get(key)
        if record:
            record["status"] = "completed"
            record["result"] = {"ok": True}
            record["expires_at"] = now() + IDEMP_TTL
```
- Do workers need shared memory? 
  - Single process (our default): no; tasks share the module-level dict with an asyncio lock.
  - Multi‑process or multi‑replica: yes; move keys to a shared store (e.g., SQLite via stdlib or Redis). See minimal SQLite example below.

Optional: TTL sweeper to bound memory
```
async def _idem_sweeper():
    while True:
        await asyncio.sleep(30)
        t = now()
        async with lock:
            for k in list(idempotency.keys()):
                if idempotency[k].get("expires_at", 0) <= t:
                    idempotency.pop(k, None)

# start at app startup
worker_tasks.append(asyncio.create_task(_idem_sweeper()))
```

SQLite-backed idempotency (single file, cross‑process)
```
import sqlite3
_db = sqlite3.connect("idem.db", check_same_thread=False)
_db.execute("CREATE TABLE IF NOT EXISTS idem (k TEXT PRIMARY KEY, status TEXT, expires REAL)")
_db.commit()

def idem_get(k: str, now_ts: float):
    row = _db.execute("SELECT status FROM idem WHERE k=? AND expires>?", (k, now_ts)).fetchone()
    return row[0] if row else None

def idem_put_accepted(k: str, ttl: float):
    _db.execute("INSERT OR REPLACE INTO idem(k,status,expires) VALUES(?,?,?)", (k, "accepted", now()+ttl))
    _db.commit()

def idem_mark_completed(k: str, ttl: float):
    _db.execute("UPDATE idem SET status=?, expires=? WHERE k=?", ("completed", now()+ttl, k))
    _db.commit()
```

Other quick pointers (already implemented)
- Backpressure (bounded queue + 429): see `queue_immediate = asyncio.Queue(maxsize=Q_MAX)` and `if queue_immediate.full(): raise HTTPException(429)`.
- Backoff + Jitter (non‑blocking): see `process_job()` computing `delay` and `await delayed_requeue(item, delay)`.
- DLQ (dead letter): see global `dlq` list, `/dlq` and `/dlq/requeue` endpoints.
- Rate limiting: global token bucket `rl_consume()` checked in `/enqueue`.

Next: [Matchmaking Queue](../matchmaking-queue/problems.md)
