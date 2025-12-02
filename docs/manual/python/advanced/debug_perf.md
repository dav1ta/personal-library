# Debugging and Performance

## Debugging

```python
# Built-in breakpoint (uses PDB by default)
def f():
    x = 1
    breakpoint()  # inspect locals
    return x + 1
```

Tracebacks and faulthandler:

```python
import traceback, faulthandler

try:
    do_work()
except Exception:
    traceback.print_exc()

faulthandler.enable()  # dumps Python traceback on fatal errors/signals
```

---

## Profiling CPU

```python
import cProfile, pstats, io

prof = cProfile.Profile()
prof.enable()
work()
prof.disable()

s = io.StringIO()
ps = pstats.Stats(prof, stream=s).sort_stats("cumulative")
ps.print_stats(20)
print(s.getvalue())
```

---

## Memory: `tracemalloc`

```python
import tracemalloc

tracemalloc.start()
work()
current, peak = tracemalloc.get_traced_memory()
print(current, peak)
```

---

## Timing: `time.perf_counter`

```python
from time import perf_counter
t0 = perf_counter()
do_task()
print(f"took {perf_counter()-t0:.3f}s")
```

---

## Tips

- Reproduce issues with smallest input; add assertions to document invariants.
- Prefer sampling profilers for production; use `cProfile` for local deep dives.
- Beware I/O in CPU profiles; isolate hot loops and measure in tight scopes.

