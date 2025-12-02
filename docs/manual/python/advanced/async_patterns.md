# Advanced Asyncio Patterns

Structured concurrency, cancellation, and flow control beyond the basics.

## TaskGroup (3.11+): Structured Concurrency

```python
import asyncio

async def fetch(url):
    await asyncio.sleep(0.1)
    return url

async def main(urls):
    results = []
    async with asyncio.TaskGroup() as tg:
        tasks = [tg.create_task(fetch(u)) for u in urls]
    # if any task errors, TaskGroup cancels siblings and re-raises
    for t in tasks:
        results.append(t.result())
    return results
```

Notes:
- Errors propagate deterministically; cleanup runs via cancellation.
- Prefer TaskGroup over manual gather for robust lifetimes.

---

## Cancellation: Deadlines and Shields

```python
import asyncio

async def bounded_call(coro, timeout):
    try:
        return await asyncio.wait_for(coro, timeout)
    except asyncio.TimeoutError:
        return None

async def critical_section():
    # prevent outer cancellation while committing
    try:
        return await asyncio.shield(do_commit())
    finally:
        await cleanup()  # still runs if shielded code cancels internally
```

Always make functions cancellation-safe: use `try/finally` for resource cleanup.

---

## Backpressure with Bounded Queues

```python
import asyncio

async def producer(q: asyncio.Queue):
    for i in range(10_000):
        await q.put(i)   # blocks when full

async def consumer(q: asyncio.Queue):
    while True:
        item = await q.get()
        try:
            await process(item)
        finally:
            q.task_done()

async def pipeline():
    q = asyncio.Queue(maxsize=100)
    async with asyncio.TaskGroup() as tg:
        tg.create_task(producer(q))
        for _ in range(8):
            tg.create_task(consumer(q))
```

Bound the queue to exert backpressure on producers and cap memory.

---

## Streams: Flow Control and `drain()`

```python
import asyncio

async def send(writer: asyncio.StreamWriter, data: bytes):
    writer.write(data)
    await writer.drain()  # cooperates with backpressure
```

Use `drain()` after writes; handle `ConnectionResetError` and cancellations to close cleanly.

---

## Async Context Managers and ExitStack

```python
from contextlib import AsyncExitStack

async def serve():
    async with AsyncExitStack() as stack:
        srv = await stack.enter_async_context(start_server())
        client = await stack.enter_async_context(open_client())
        await run(srv, client)
```

Manage multiple async resources robustly; ensures LIFO cleanup.

