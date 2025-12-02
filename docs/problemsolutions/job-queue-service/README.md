# job-queue-service

Minimal Python service for exploring a job queue with workers: enqueue via HTTP, bounded worker pool, simple stats.

Run (no deps):

```
python3 src/app.py
```

Endpoints:
- `POST /enqueue` — body JSON payload; returns 202 or 429 if queue full
- `GET /stats` — queue depth, processed, failures, worker count

Notes:
- This is an MVP using only the Python standard library.
- See `problems.md` for scenarios (retries, DLQ, idempotency, etc.).

FastAPI version (more features)
- Run: `pip install fastapi uvicorn pydantic` then `uvicorn job-queue-service.src.fastapi_app:app --reload`
- Endpoints:
  - `POST /enqueue` with optional headers:
    - `X-Idempotency-Key`: dedupe enqueues (memory by default; set `IDEMP_BACKEND=sqlite` to share across processes)
    - `X-Client-Id`: per-client rate limiting
  - `GET /stats`: JSON counters/gauges
  - `GET /metrics`: Prometheus exposition (job_* and rate_* metrics)
- Env options:
  - `IDEMP_BACKEND=sqlite` and `IDEMP_SQLITE_PATH=idem.db` to enable shared idempotency via SQLite
