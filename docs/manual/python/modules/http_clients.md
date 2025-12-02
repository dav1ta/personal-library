# HTTP Clients: Robust Patterns

Covering `requests` (sync) and `httpx` (sync/async) with timeouts, retries, streaming, and TLS configuration.

## Requests: Sessions, Timeouts, Retries

```python
import requests
from urllib3.util.retry import Retry
from requests.adapters import HTTPAdapter

session = requests.Session()
retries = Retry(
    total=5,
    backoff_factor=0.5,
    status_forcelist={429, 500, 502, 503, 504},
    allowed_methods={"GET", "POST"},
)
session.mount("https://", HTTPAdapter(max_retries=retries))

resp = session.get("https://example.com/data", timeout=(2, 10))  # connect, read
resp.raise_for_status()
data = resp.json()
```

Streaming download to file:

```python
with session.get(url, stream=True, timeout=(2, 30)) as r:
    r.raise_for_status()
    with open("payload.bin", "wb") as f:
        for chunk in r.iter_content(chunk_size=64 * 1024):
            if chunk:
                f.write(chunk)
```

---

## HTTPX: Async + Sync, Timeouts, TLS

```python
import httpx, asyncio

async def fetch(url: str):
    limits = httpx.Limits(max_keepalive_connections=20, max_connections=100)
    timeout = httpx.Timeout(5.0, connect=2.0)
    async with httpx.AsyncClient(limits=limits, timeout=timeout, http2=True) as client:
        r = await client.get(url)
        r.raise_for_status()
        return r.json()

asyncio.run(fetch("https://api.example.com"))
```

Custom trust store:

```python
import httpx, ssl

ctx = ssl.create_default_context(cafile="/etc/ssl/certs/custom.pem")
with httpx.Client(verify=ctx) as client:
    client.get("https://internal")
```

---

## Backoff with Jitter

```python
import random, time

def backoff(attempt, base=0.2, cap=10.0):
    return min(cap, base * (2 ** attempt)) * (0.5 + random.random()/2)
```

Use jitter to prevent thundering herds; combine with idempotency keys on POSTs.

---

## Tips

- Always set timeouts; defaults can hang indefinitely.
- Reuse sessions/clients for connection pooling.
- Validate SSL/TLS and pin CA bundles where appropriate.
- For large uploads/downloads, stream in chunks and propagate backpressure.

