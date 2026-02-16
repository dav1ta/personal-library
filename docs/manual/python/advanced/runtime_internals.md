# Python Runtime Internals

Missing topics from `to_distribute.md` that did not have related docs.

## Language Edge Cases

### Exception variable scope deletion
`except Exception as e` binds `e` only for the handler block. CPython clears it after the block to break traceback reference cycles.

```python
try:
    1 / 0
except ZeroDivisionError as e:
    err = str(e)

print(err)   # ok
# print(e)   # NameError
```

### `+=` vs `+` for mutable types
`+` always creates a new object. `+=` tries in-place update (`__iadd__`) first.

```python
a = [1, 2]
b = a
a = a + [3]    # new list; b unchanged

c = [1, 2]
d = c
c += [3]       # mutates same list; d also sees [1, 2, 3]
```

### Late binding in closures
Closures capture variables, not their historical values. In loops, all closures may see the final loop value unless you freeze it with default args.

### Mutable default arguments
Defaults are evaluated once at function definition time. Use `None` sentinel + create new object inside when you want fresh state.

### `is` vs `==`, interning, and caches
`is` checks identity. `==` checks value equality. Small ints and some strings can be interned/cached, so identity behavior is implementation detail.

### Dict insertion order guarantee (3.7+)
Insertion order is guaranteed by language spec in Python 3.7+. It is stable for iteration, but hash randomization can still affect hash-dependent behavior.

### Class body execution time
Class body executes immediately at definition, creating class namespace before `type(...)` finalizes class object.

### C3 linearization and MRO
Multiple inheritance uses C3 linearization to produce a monotonic method resolution order that preserves local precedence.

### Function defaults in `__defaults__`
Positional defaults are stored in function object `__defaults__`; keyword-only defaults in `__kwdefaults__`.

### Structural pattern matching edge cases
Order matters. Earlier broad patterns can shadow later specific ones; guards run after structural match.

### Sequence pattern vs class/type pattern
Sequence patterns match protocol/shape; class patterns use class-specific matching (`__match_args__`) and attribute extraction.

### `__slots__` and inheritance layout conflicts
Mixing slotted and non-slotted bases or conflicting slot names can create layout issues and errors in multiple inheritance.

### `__del__` hazards and resurrection
Finalizers can run in fragile interpreter states and can resurrect objects by storing `self` somewhere global.

### Hash randomization and determinism
`PYTHONHASHSEED` controls randomized hash seeding. Pin it for deterministic tests that rely on hash-sensitive behavior.

## Performance and Bytecode

### `LOAD_FAST` vs `LOAD_GLOBAL`
`LOAD_FAST` reads function locals from a compact array-like storage. `LOAD_GLOBAL` performs module/global/builtins lookup, which is slower.

### Why binding globals locally speeds things up
Local lookup (`LOAD_FAST`) is cheaper than global lookup (`LOAD_GLOBAL`) because globals require dict/module resolution.

### Disassembling bytecode with `dis`
Use `dis.dis(fn)` to inspect opcode-level behavior and verify assumptions.

### Constant folding and compile-time optimizations
The compiler folds simple constants (`2 * 3` -> `6`) and some literal expressions before runtime.

### Stack-based VM design in CPython
CPython executes bytecode on an evaluation stack. Opcodes push/pop values instead of operating on registers.

### Frame object overhead
Every Python call creates a frame (locals, globals, instruction pointer, traceback linkage). Many tiny calls increase overhead.

### Why tail call optimization is intentionally absent
Python keeps full stack traces for debugging and introspection, so tail-call elimination is intentionally not applied.

### Inline caches and adaptive specialization (3.11+)
CPython 3.11 adds inline caches and specializes hot opcodes at runtime (PEP 659), reducing repeated dynamic lookup costs.

### Attribute lookup caching in 3.11+
Repeated attribute loads can be specialized with inline caches keyed by object/type shape and dictionary versioning.

### Vectorcall protocol (PEP 590)
A fast call protocol used by many built-ins/C extensions to reduce temporary tuple/dict allocations at call boundaries.

### Call argument tuple/dict allocation costs
Frequent `*args/**kwargs` or wrapper layers can add allocation overhead in hot paths.

### Why iteration is faster than indexing loops
`for x in seq` uses iterator protocol and avoids repeated index operations and bounds checks in Python space.

### Dict resizing and load factor strategy
Dicts resize to keep lookup fast (amortized O(1)); heavy insert/delete workloads may trigger rehash/resizing costs.

### Tombstones in dict deletion
Deleted slots leave tombstones that keep probe chains valid; too many tombstones degrade lookups until resize/rehash.

### Shared-key dicts for instances
Instances of the same class can share key tables for `__dict__`, reducing per-instance memory.

### Object allocation cost vs C-structure access
Python objects are boxed and reference-counted; operations on C-level compact buffers (arrays, vectorized libs) are cheaper.

### Microbenchmark warm-up (adaptive interpreter)
Run short warm-up loops before timing so specialization/caches settle.

## Concurrency and Async Internals

### `Lock` vs `RLock`
`threading.Lock` is not reentrant. `threading.RLock` lets the same thread acquire multiple times with matched releases.

### Why `asyncio.Lock` is not reentrant
`asyncio.Lock` is task-coordination, not ownership-recursive locking. Reentrancy can hide deadlocks and ordering bugs.

### `await` as generator machinery
Coroutines compile to state machines with suspension points. `await` yields control to the event loop until the awaited object is ready.

### Event loop internals: selector + queues
Loop core is usually: I/O selector, ready callback queue, scheduled timer queue, and task/future state transitions.

### Context switching cost in asyncio
Each `await` can reschedule tasks and callbacks. Too many tiny awaits can dominate runtime.

### Async is concurrency, not parallelism
One thread/event loop interleaves tasks; CPU-bound work still needs processes/native code for real parallel speedup.

### GIL mechanics (switch interval and scheduling)
The GIL permits one thread executing Python bytecode at a time. Threads can switch at periodic check points and around blocking operations.

### Where the GIL is released
Commonly during blocking I/O and in C extensions that explicitly release it around long native work.

### Why CPU-bound threads do not scale
CPU-bound threads compete under the GIL, so throughput often does not improve with more threads for pure Python compute loops.

### ProcessPool vs ThreadPool tradeoff
ThreadPool is cheap and good for I/O-bound work. ProcessPool bypasses GIL for CPU-bound work at higher IPC/serialization cost.

### Why profiling changes behavior
Tracing/profiling adds hooks around calls/lines, changing timing and potentially branch/cache behavior.

## Memory and Object Internals

### Reference counting basics
Every object tracks a reference count; when it reaches zero, object memory can be reclaimed immediately (outside cycles).

### Generational cyclic GC
Cyclic GC supplements refcounting by collecting reference cycles with generation heuristics.

### Refcount and cyclic GC interaction
Refcount handles most short-lived objects fast; cyclic GC periodically scans containers for unreachable cycles.

### `PyLongObject` (boxed integers)
Python integers are heap objects with metadata and digit arrays, not raw machine integers.

### Disabling GC and leak risk
Turning off cyclic GC can improve short hot loops, but long-lived cycles will accumulate.

### `obmalloc`: arenas, pools, blocks
CPython uses a small-object allocator layered over arenas/pools/blocks for speed and locality.

### Why memory is not always returned to OS
Freed objects may return to Python allocators, not directly to the OS, especially for fragmented arenas.

### Fragmentation vs true leaks
Growth can come from allocator fragmentation even when objects are collectible; verify with snapshots before calling it a leak.

### Frame objects are heap allocations
Frames are objects with links to code, locals, and prior frames; deep/recursive call stacks increase memory pressure.

### Object header layout (`PyObject`)
Every object carries header metadata (refcount, type pointer), which is part of Python's per-object overhead.

### Small-integer cache and interned strings
CPython reuses common small integers and can intern strings to reduce allocation and speed comparisons.

### Identity vs equality at memory level
Identity compares object address-level identity (`is`), equality compares semantic value (`==`) via type-defined comparison logic.

### List multiplication duplicates references
`[[0] * 2] * 3` duplicates references to same inner object, not deep copies.

## Object Model, Import Runtime, and Introspection

### Function objects as descriptors
Functions implement descriptor behavior so `obj.method` produces a bound method with `self` attached.

### Bound method creation mechanics
Attribute access on function descriptors creates transient bound-method objects that wrap `(function, instance)`.

### `staticmethod` and `classmethod`
`staticmethod` disables binding; `classmethod` binds the class object as first argument.

### `__prepare__` and class namespace control
Metaclasses can customize class body namespace creation via `__prepare__`, influencing definition-time behavior/order handling.

### Class creation step by step
Rough flow: resolve metaclass -> call `__prepare__` -> execute class body -> call metaclass `__new__/__init__` -> publish class.

### Modifying classes at runtime
Classes are mutable objects; assigning attributes after definition changes future lookups for instances.

### Descriptor-based ORM field systems
ORM fields often use descriptors to intercept get/set and translate attribute access into validation/query behavior.

### Overriding attribute access globally
Custom `__getattribute__` can intercept all attribute access, but must delegate carefully to avoid recursion.

### Monkeypatching implications
Monkeypatching is useful in tests and hotfixes but can create hidden coupling and order-dependent behavior.

### Dataclass internals and `default_factory`
Dataclasses generate methods at class creation; mutable defaults must use `default_factory` to avoid shared state bugs.

### Runtime code injection via `__code__`
Replacing function `__code__` swaps implementation at runtime; signatures/freevars/closure expectations must remain compatible.

### Import-time execution model
Module top-level code runs at import once per process (then cached in `sys.modules`).

### Module caching in `sys.modules`
Imports reuse cached module objects from `sys.modules`, which is why repeated imports are cheap and stateful.

### Import machinery (`sys.meta_path`, finders/loaders)
Import resolves module specs through meta path finders, then loaders create/exec modules.

### Reloading and stale references
`importlib.reload` re-executes module code, but existing references held elsewhere still point to old objects.

### Circular import behavior
Circular imports can expose partially initialized modules; defer imports or restructure dependency edges to avoid fragile state.

### `.pyc` caching
Compiled bytecode is cached in `__pycache__`; invalidation uses timestamp/hash strategy depending on mode.

### Startup bootstrapping, frozen modules, `site`
Interpreter startup initializes core runtime, loads bootstrap/frozen modules, then executes `site` (unless disabled) to configure paths/hooks.

### Accessing locals/globals at runtime
Use `locals()` and `globals()` for introspection/debugging. Writes to `locals()` are not reliable for mutating active function locals.

### Inspecting stack frames
`inspect` and frame APIs expose call-site metadata, arguments, and source context for diagnostics.

### Frame linking and call chains
Frames link to previous frames (`f_back`), enabling traceback walking and call-chain inspection.

### `sys.settrace` and `sys.setprofile`
Both install interpreter hooks; powerful for debuggers/profilers but expensive and can disable/suppress optimizations.

### Why debugging can disable/suppress optimizations
Instrumentation hooks force extra interpreter bookkeeping and can prevent adaptive hot-path behavior from reflecting production execution.

### Introspection as a design priority
Python intentionally exposes rich runtime metadata (`__dict__`, frames, code objects, MRO), trading some performance for debuggability.

### Traceback manipulation
Tracebacks are linked frame snapshots. You can inspect or re-raise with explicit traceback for debugging/reporting flows.

## Hardcore Topics (Roadmap)

These are usually implemented in small experiments:
- Minimal interpreter: tokenize/parse/eval a tiny language.
- Custom event loop or coroutine scheduler: selector + ready queue + task state machine.
- CPython dispatch loop/computed goto: study opcode dispatch strategy in `ceval`.
- AST compilation pipeline: source -> tokens -> AST -> symtable -> bytecode.
- Bytecode rewriting: transform code objects carefully; preserve flags/freevars/stack invariants.
- `tp_traverse`: implement GC traversal for container-like extension types.
- Memory pool allocator: arena/pool/block bookkeeping for fixed-size chunks.
- Descriptor-driven mini ORM: field descriptors + metaclass registry + query builder.
- PEP 659 internals: understand specialization counters and opcode rewriting.
- Manual ref management custom object: C extension type with careful INCREF/DECREF discipline.
- PyPy vs CPython: tracing JIT/runtime specialization vs bytecode interpreter with C API constraints.

Next: [Debug & Performance](debug_perf.md)
