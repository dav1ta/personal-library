# FastAPI Troubleshooting (Quick)

## CORS Blocked
```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://example.com"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

## DB Connection Errors
- Ensure pool settings and timeouts are sane.
- Use `pool_pre_ping=True` for stale connections.

## JWT/Auth Failures
- Validate token signature + expiry.
- Ensure clock skew is handled (small leeway).
- Return 401 on invalid token.

## Slow Endpoints
- Check blocking calls inside `async` handlers.
- Offload CPU work to thread pools.
- Add pagination and caching.

## Validation Errors
- Inspect `exc.errors()` in a custom exception handler.

## Large Uploads
- Stream uploads; avoid reading large files into memory.

## Debugging
- Use structured logging.
- Run with `uvicorn --reload` in dev only.
