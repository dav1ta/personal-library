# Multiprocessing Deep Dive

Processes bypass the GIL for CPU-bound work. This guide covers start methods, shared memory, IPC, pools, and common pitfalls.

## Start Methods and Safety

```python
import multiprocessing as mp

def worker(x):
    return x * x

if __name__ == "__main__":
    mp.set_start_method("spawn")  # explicit and safest cross-platform
    with mp.Pool(processes=4) as pool:
        print(pool.map(worker, range(8)))
```

Notes:
- `spawn` (default on Windows/macOS): imports the module fresh; everything passed must be picklable.
- `fork` (default on many Linux): copies the parent memory; beware copying locks/threads/sockets.
- `forkserver`: clean fork from a server process; safer than `fork` in threaded apps.

Prefer `spawn`/`forkserver` for correctness in mixed-thread programs.

---

## Shared Memory and Zero-Copy

```python
from multiprocessing import shared_memory
import numpy as np  # only for demonstration; not required

# Producer
shm = shared_memory.SharedMemory(create=True, size=1024)
buf = np.ndarray((256,), dtype=np.uint8, buffer=shm.buf)
buf[:] = np.arange(256)
print(shm.name)  # pass this string to consumers

# Consumer
shm2 = shared_memory.SharedMemory(name=shm.name)
view = memoryview(shm2.buf)[:10]
print(bytes(view))

shm.close(); shm.unlink()  # unlink when last user is done
shm2.close()
```

Use `SharedMemory` for large arrays/buffers without pickling overhead. Manage lifetime with `unlink()`.

---

## IPC: Queues, Pipes, Managers

```python
import multiprocessing as mp

def worker(q: mp.Queue):
    q.put({"msg": "hello"})

if __name__ == "__main__":
    mp.set_start_method("spawn")
    q = mp.Queue(maxsize=16)
    p = mp.Process(target=worker, args=(q,))
    p.start()
    print(q.get())
    p.join()
```

For proxying complex objects, `multiprocessing.Manager()` provides shared dict/list proxies at the cost of more overhead.

---

## Process Pools vs Executors

```python
from concurrent.futures import ProcessPoolExecutor

def cpu_task(n: int) -> int:
    # heavy CPU work
    ...

if __name__ == "__main__":
    with ProcessPoolExecutor(max_workers=4, mp_context=mp.get_context("spawn")) as ex:
        futs = [ex.submit(cpu_task, i) for i in range(8)]
        results = [f.result() for f in futs]
```

Prefer `ProcessPoolExecutor` for modern APIs and composability with `ThreadPoolExecutor`.

---

## Pitfalls

- Guard entry with `if __name__ == "__main__":` when using `spawn`.
- Ensure targets are at module top-level (picklable); avoid lambdas/inner functions.
- Avoid holding global connections (DB, sockets) across fork; reinitialize in child.
- Control memory growth: big args/results are pickled; consider shared memory or mmap.

