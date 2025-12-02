# Memory, GC, and Zero-Copy Patterns

Deep dive into CPython refcounting, the cyclic GC, weakrefs/finalizers, and buffer-protocol tricks.

## Refcounting + Cyclic GC

CPython frees most objects immediately when their reference count hits zero. Cycles are collected later by the cyclic GC.

```python
import gc, weakref

class Node:
    def __init__(self):
        self.ref = None
    def __del__(self):  # dangerous in cycles
        pass

a = Node(); b = Node()
a.ref = b; b.ref = a   # cycle
del a, b
gc.collect()           # needed for cycles; __del__ in cycles can delay
```

Pitfall: objects in cycles that define `__del__` may be left uncollected (prior to PEP 442) or have delayed finalization. Prefer `weakref.finalize` instead of `__del__`.

---

## Leak Hunting with `gc` and `tracemalloc`

```python
import gc, tracemalloc

def snapshot_top(n=5):
    snap = tracemalloc.take_snapshot()
    for stat in snap.statistics("lineno")[:n]:
        print(stat)

gc.set_debug(gc.DEBUG_SAVEALL)
tracemalloc.start()

# run workload
snapshot_top()

# inspect uncollectable objects
gc.collect()
print("uncollectable:", len(gc.garbage))
```

Tips:
- Diff snapshots with `compare_to` to isolate hotspots.
- Watch for lingering global references, lru_caches, and closures keeping large objects alive.

---

## Weak References and Finalizers

```python
import weakref

class Cache:
    def __init__(self, obj):
        self.ref = weakref.ref(obj)

def on_close(path):
    print("cleanup", path)

class Resource:
    pass

r = Resource()
fin = weakref.finalize(r, on_close, "/tmp/file")
del r  # calls on_close when collected
```

Prefer `finalize` over `__del__` for predictable cleanup without cycle issues.

---

## `__slots__` to Save Memory

```python
class WithDict:
    def __init__(self, x, y):
        self.x, self.y = x, y

class Slotted:
    __slots__ = ("x", "y")
    def __init__(self, x, y):
        self.x, self.y = x, y
```

`__slots__` removes per-instance `__dict__`, reducing memory and speeding attribute access. Avoid if you need dynamic attributes or multiple inheritance without care.

---

## Zero-Copy with Buffer Protocol

Use `memoryview` and `readinto` to avoid allocating intermediate objects.

```python
import io

buf = io.BytesIO(b"\x00\x01\x02\x03\x04\x05")
data = bytearray(4)
buf.readinto(data)            # fills preallocated buffer
mv = memoryview(data)
head, tail = mv[:2], mv[2:]
tail[:] = b"ZZ"               # mutate in-place
```

Combine with `array`, `mmap`, `numpy` (third-party) for large binary pipelines.

---

## Temporarily Disabling GC

For short-lived, allocation-heavy sections, disabling the cyclic GC can improve throughput; re-enable promptly.

```python
import gc

old = gc.isenabled()
gc.disable()
try:
    hot_loop()
finally:
    if old: gc.enable()
```

Measure before and after; benefits are workload dependent.

---

## Object Size Caveats

`sys.getsizeof(obj)` returns only the immediate object’s size, not children. For deep sizes, traverse containers or use specialized profilers.

