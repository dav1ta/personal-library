
# Python Quirks, Gotchas & Advanced Facts

A curated, structured tour of Python’s surprising behaviors and powerful internals. Use the contents and thematic map to jump to areas you’re learning.

## Contents
- Basics & Medium-Level Quirks
- Harder, Guru-Tier Facts
- Deep Python Internals & Obscure Facts
- More Advanced Python-Level Facts (Beyond CPython Internals)
- Advanced Python: AST Transforms & Obscure Protocols
- “Read About” Areas and Where To Go Next

## Thematic Map (Quick Navigation)
- Basics: identity vs equality, unpacking, control-flow else, with, REPL `_`.
- Functions: defaults, decorators, keyword-only args, annotations, callable objects.
- Classes/Object model: `type`, MRO, `super`, `__new__`, `__class__`, descriptors, `__slots__`.
- Collections/Iteration: negative indexing, iterators, generators, `yield from`, dict order.
- Numbers/FP: bool as int, big ints, floating-point quirks, NaN.
- Scopes/Namespaces: `global` vs `nonlocal`.
- Exceptions/Context: for/try else, context managers, chaining.
- Introspection/Internals: frames, code objects, tracing, GC, `id`, `vars`, `__dict__`.
- Import system: `import` as code, `__import__`, meta hooks, reload.
- Protocols: formatting, comparisons, ABCs, `__contains__`, slice, truthiness, dataclasses, enums.
- Async: async iterators/context, coroutines, awaitable protocol.
- AST & metaprogramming: parse/transform/compile AST, instrument at import.

---

## 🟢 Basics & Medium-Level Quirks

### 1. `staticmethod` can’t be subclassed
A `staticmethod` is just a plain function in a class namespace. No polymorphism.

```python
class A:
    @staticmethod
    def f(): return "A"

class B(A):
    @staticmethod
    def f(): return "B"

print(A.f())  # "A"
print(B.f())  # "B"
```

---

### 2. Default arguments are evaluated once
Mutable defaults persist across calls.

```python
def f(x, lst=[]): lst.append(x); return lst
print(f(1), f(2))  # [1] [1,2]
```

---

### 3. `is` ≠ `==`
`is` checks identity, not equality.

```python
print([] is [])   # False
print([] == [])   # True
```

---

### 4. `bool` ⊂ `int`
Booleans behave as integers.

```python
print(True + True)  # 2
```

---

### 5. Small integers and some strings are cached
CPython interns some strings and caches small ints.

```python
print(256 is 256)   # True (cached small int)
print(257 is 257)   # False (implementation detail)
print("hi" is "hi")  # May be True (interned)
```

---

### 6. Negative indexing
`-1` means `len-1`.

```python
nums = [1,2,3]
print(nums[-1])  # 3
```

---

### 7. Functions are rebinding
Later definitions overwrite earlier ones.

```python
def f(): return 1
def f(): return 2
print(f())  # 2
```

---

### 8. `__del__` isn’t guaranteed
Objects in cycles with `__del__` may never be collected.

---

### 9. `__slots__` save memory
Removes `__dict__`; prevents creating new attributes unless slotted.

```python
class A: __slots__ = ('x',)
a = A(); a.x = 1
```

---

### 10. `NaN != NaN`
IEEE 754: NaN is not equal to itself.

```python
import math
print(math.nan == math.nan)  # False
```

---

### 11. Chained comparisons
Work like math.

```python
print(1 < 2 < 3)  # True
```

---

### 12. `for` loop has an `else`
Executes if no `break`.

```python
for i in range(3):
    if i == 5: break
else:
    print("No break hit")
```

---

### 13. `try` also has `else`
Runs if no error.

```python
try:
    x = 1
except:
    pass
else:
    print("No error")
```

---

### 14. `with` can be nested
One-liner.

```python
with open("a.txt") as f1, open("b.txt") as f2:
    ...
```

---

### 15. Iterators are one-shot
Consumed after one pass.

```python
it = iter([1,2])
list(it)
list(it)  # []
```

---

### 16. Generators accept input
Use `.send()`.

```python
def g():
    x = yield
    print(x)

gen = g(); next(gen)
gen.send(42)  # prints 42
```

---

### 17. Tuple unpacking magic
Unpack with `*`.

```python
a, b, *rest = [1,2,3,4]
print(rest)  # [3,4]
```

---

### 18. `_` holds last REPL result
Interactive only.

---

### 19. Dicts preserve insertion order
Guaranteed language feature since 3.7.

---

### 20. `global` vs `nonlocal`
Different scopes.

---

### 21. You can subclass built-ins
Even `int`.

---

### 22. Everything is an object
Even functions.

---

### 23. Recursion limit
~1000 by default.

---

### 24. `True` and `False` are constants
In Python 3, they can’t be rebound.

---

### 25. `...` (Ellipsis) is real
Actual object.

---

---

## 🔵 Harder, Guru-Tier Facts

### 26. Class body executes immediately
Runs like a script.

```python
print("before class")
class A: print("inside class")
print("after class")
```

---

### 27. `type` is both a class and metaclass
Everything is an instance of `type`.

---

### 28. Metaclass conflict
Different metaclasses → `TypeError`.

---

### 29. `__new__` before `__init__`
You can replace the instance.

```python
class A:
    def __new__(cls): return []
print(isinstance(A(), list))  # True
```

---

### 30. Descriptors power property
Any object with `__get__`, `__set__`.

---

### 31. `super()` follows MRO
Not strict parent.

---

### 32. `__mro__` shows order
Uses C3 linearization.

---

### 33. `classmethod` respects subclass
Unlike `staticmethod`.

---

### 34. `__call__` makes objects callable
Callable classes.

---

### 35. Hashability depends on contents
Tuples are hashable if items are.

---

### 36. `__getattr__` vs `__getattribute__`
- `__getattribute__` always  
- `__getattr__` only if missing  

---

### 37. Integers are arbitrary precision
Never overflow.

---

### 38. `0.1+0.2 != 0.3`
Floating-point precision.

---

 

### 42. Decorators run at definition
Not at call.

---

### 43. `__dict__` and `vars()`
Objects with attributes expose them.

---

### 44. `id()` is not memory address
Implementation detail.

---

### 45. GC = refcount + cycles
CPython frees instantly except cycles.

---

### 46. `__class__` can be reassigned
Live mutation of type.

---

### 47. `yield from` delegates
Flattens generators.

---

### 48. Keyword-only arguments
Enforce keyword use.

---

### 49. Annotations are metadata only
No enforcement.

---

### 50. `import` is executable code
You can import conditionally.

```python
def f():
    import math
    return math.pi
```

---

# 🧠 Deep Python Internals & Obscure Facts

Here’s the next layer — **stuff hidden in CPython internals, runtime tricks, and metaprogramming corners.**  
Not beginner-usable, but real “guru” level quirks.

---

### 51. `sys._getframe()` gives you the call stack
You can inspect or even walk up scopes.

```python
import sys
def f(): return sys._getframe().f_code.co_name
print(f())  # 'f'
```

---

### 52. Function code lives in `.__code__`
You can mutate it (dangerous).  

```python
def f(): return 1
def g(): return 2
f.__code__ = g.__code__
print(f())  # 2
```

---

### 53. `__closure__` stores captured variables
Closures capture variables, not values.

```python
def outer():
    x = 10
    def inner(): return x
    return inner

f = outer()
print(f.__closure__[0].cell_contents)  # 10
```

---

### 54. `compile` + `eval` = runtime code generation
Python can build functions dynamically.

```python
code = compile("x+1", "<expr>", "eval")
print(eval(code, {}, {"x": 5}))  # 6
```

---

### 55. Everything importable is just a module object
Modules are singletons in `sys.modules`.

```python
import math, sys
print(sys.modules["math"])  # <module 'math'>
```

---

### 56. `importlib.reload` re-executes modules
Dangerous but real.

---

### 57. You can hook imports
Install a custom finder/loader via `sys.meta_path`. That’s how `import` magic libraries work.

---

### 58. Python has a “frame locals proxy”
Inside a frame, `f_locals` is a **snapshot** — updating it doesn’t guarantee live variable changes.

```python
import sys
def f():
    x = 10
    frame = sys._getframe()
    frame.f_locals["x"] = 99
    print(x)  # Still 10 (optimizer!)
f()
```

---

### 59. Garbage collector exposes unreachable
`gc.garbage` stores objects with `__del__` that couldn’t be freed.

---

### 60. `weakref` lets you reference without ownership
GC doesn’t count weakrefs.

```python
import weakref
class A: pass
a = A()
w = weakref.ref(a)
print(w())  # <__main__.A object>
del a
print(w())  # None
```

---

### 61. `__import__` is the real import function
`import` is syntax sugar.

```python
m = __import__("math")
print(m.sqrt(9))  # 3
```

---

### 62. `inspect` reveals function source
If available on disk.

```python
import inspect, math
print(inspect.getsource(abs))  # may raise (C funcs lack source)
```

---

### 63. Python functions can be introspected deeply
Attributes:  
- `__code__` (bytecode, argcount, varnames)  
- `__defaults__` (defaults)  
- `__kwdefaults__` (keyword defaults)  
- `__annotations__`  

---

### 64. Bytecode is accessible
`dis` shows it.

```python
import dis
def f(x): return x+1
dis.dis(f)
```

---

### 65. You can monkeypatch builtins
Dangerous but works.

```python
import builtins
builtins.print = lambda *a, **k: None
print("won't show")
```

---

### 66. `exec` can alter locals/globals
Dynamic code injection.

```python
ns = {}
exec("x=42", ns)
print(ns["x"])  # 42
```

---

### 67. `sys.settrace` lets you trace every line
How debuggers/profilers are built.

```python
import sys
def trace(frame, event, arg):
    print(event, frame.f_code.co_name)
    return trace
sys.settrace(trace)

def f(): return 123
f()
sys.settrace(None)
```

---

### 68. `sys.setprofile` hooks function calls
Less granular than `settrace`.

---

### 69. Python opcodes are visible (not customizable at runtime)
`dis` shows opcodes like `LOAD_FAST`, `CALL`. Custom opcodes require rebuilding CPython.

---

### 70. `atexit` runs at process end
Even if no `finally`.

```python
import atexit
atexit.register(lambda: print("bye"))
```

---

### 71. `__enter__` / `__exit__` power `with`
That’s just a protocol.

---

### 72. Async context managers use `__aenter__` / `__aexit__`
Same idea but `await`-aware.

---

### 73. Async iterators are protocols too
`__aiter__`, `__anext__`.

---

### 74. Metaclasses can rewrite class body
`__prepare__` can supply a custom mapping (e.g., an `OrderedDict`, or a validating dict).

---

### 75. Functions are descriptors
That’s why methods bind automatically.  

```python
class A:
    def f(self): return 123
print(A.f)     # function
print(A().f)   # bound method
```

---

### 76. CPython refcounts are visible
`sys.getrefcount(obj)` includes the temp arg reference.

```python
import sys
x = []
print(sys.getrefcount(x))  # usually 2
```

---

### 77. Python caches small integers and may intern strings
Small ints `[-5, 256]` cached; adjacent string literals merge at compile time.

```python
s = "hello " "world"
print(s)  # "hello world"
```

---

### 79. Generator `close()` raises `GeneratorExit`
Code inside can trap it.

---

### 80. Exception context chaining
`raise ... from ...` sets `__cause__`. Suppressing it uses `raise ... from None`.

---

---

# 🧠 More Advanced Python-Level Facts (Beyond CPython Internals)

Sticking to **Python-visible behavior** (no digging into C source).  
These are things an expert Pythonista should know and can actually *use*.

#### Containers & Iteration

---

### 111. `__iter__` and `__reversed__`
If you implement `__iter__`, `reversed(obj)` will still fail unless you also implement `__reversed__` or support `__len__` and `__getitem__`.

```python
class A:
    def __iter__(self): return iter([1,2,3])
print(list(reversed(A())))  # TypeError
```

#### Formatting & Conversion

### 112. `__format__` customizes f-strings
Objects can control how they’re formatted in f-strings.

```python
class Money:
    def __format__(self, spec): return f"${123:.2f}"
print(f"{Money():>10}")  # right-aligned custom output
```

---

### 113. `__round__`, `__trunc__`, `__floor__`, `__ceil__`
Numeric protocols allow customizing math builtins.

---

### 114. `__index__` for integer contexts
If an object defines `__index__`, it can be used in slicing and `hex()`, `bin()`.

```python
class Index:
    def __index__(self): return 5
print([1,2,3,4,5,6][Index():])  # [6]
```

---

### 115. `__bytes__` and `__complex__`
Like `__str__` and `__int__`, but for bytes and complex conversions.

---

### 116. `__contains__` vs `__iter__`
`x in obj` first checks `__contains__`.  
If missing, it falls back to iteration.

#### Operators & Comparisons

### 117. Operator overloading covers *a lot*
Examples:  
- `__matmul__` → `@` operator  
- `__pow__` → `**`  
- `__invert__` → `~`  
- `__radd__` → right-hand addition  

---

### 118. Rich comparisons are asymmetric
If `a.__lt__(b)` returns `NotImplemented`, Python calls `b.__gt__(a)`.

---

### 119. `functools.total_ordering`
Fills in missing comparison methods automatically if you define at least one.

#### ABCs & Virtual Subclassing

### 120. Abstract Base Classes (`abc`)
You can register classes *virtually* without inheritance.

```python
from collections.abc import Sequence
class MySeq: pass
Sequence.register(MySeq)
print(issubclass(MySeq, Sequence))  # True
```

#### Introspection & Docs

### 121. `__doc__` is writable
You can patch docstrings at runtime.

```python
def f(): pass
f.__doc__ = "Patched"
print(f.__doc__)
```

---

### 122. `__annotations__` is a dict
Type hints live here. They’re not enforced.

---

### 123. Module-level `__getattr__`
Since Python 3.7, modules can define `__getattr__` for lazy imports or deprecations.

```python
# mymod.py
def __getattr__(name):
    if name == "old": return 42
    raise AttributeError
```

---

### 124. Class variables can be descriptors too
Even `staticmethod`/`classmethod` are just descriptors.

---

### 125. Properties are class attributes
They live on the class, not the instance. Instances just trigger the descriptor.

---

### 126. `classmethod` works on metaclasses
Yes, you can bind to the metaclass itself.

---

### 127. Functions can have arbitrary attributes
They’re just objects.

```python
def f(): pass
f.foo = 123
print(f.foo)
```

---

### 128. `globals()` and `locals()` are dict-like
Mutating `locals()` inside functions is not guaranteed to affect real locals.

---

### 129. `__dir__` can be customized
Controls what `dir(obj)` shows.

#### Pickling & Hashing

### 130. Pickle uses `__reduce__`
Pickling/unpickling protocol is fully customizable.

---

### 131. `hash()` must agree with `==`
If two objects are equal, their hashes must match — otherwise dicts/sets break.

---

### 132. `frozenset` is hashable
That’s how you can have sets of sets.

---

### 133. `object.__setattr__` and `__delattr__`
Even in frozen dataclasses, you can bypass immutability by calling base methods directly.

#### Builtins & Control Flow

### 134. `__builtins__` scope is per-module
Different modules may see different `__builtins__` references.

---

### 135. `try/finally` always runs finally
Even if you `os._exit()`? No — only if interpreter exits normally.  

---

### 136. `with` blocks can suppress exceptions
If `__exit__` returns `True`, the exception is swallowed.

```python
class Suppress:
    def __enter__(self): pass
    def __exit__(self, *a): return True

with Suppress(): 1/0  # no crash
```

---

### 137. `__eq__` without `__hash__` makes objects unhashable
If you override `__eq__`, Python disables `__hash__` by default.

#### Decorators & Coroutines

### 138. Decorators are just sugar
`@deco def f(): ...` is just `f = deco(f)`.

---

### 139. Coroutines can be introspected
`inspect.iscoroutinefunction`, `inspect.getcoroutinestate`.

---

### 140. `async for` and `async with` are protocols
They use `__aiter__`, `__anext__`, `__aenter__`, `__aexit__`.

#### Container & Truthiness Protocols

### 141. `__delitem__` drives `del obj[key]`
You can intercept deletions.

---

### 142. Slice objects are real
`obj[1:5:2]` creates a `slice(1,5,2)` object.

```python
s = slice(1,5,2)
print(s.start, s.stop, s.step)
```

---

### 143. `Ellipsis` (`...`) is often used as a placeholder
That’s why you see it in stubs / type hints.

---

### 144. Boolean operators short-circuit
`a and b` doesn’t call `__and__`, it uses truthiness. Bitwise `&` calls `__and__`.

---

### 145. Truthiness is protocol-based
Objects can define `__bool__`. If missing, `__len__` decides.

#### Dataclasses, Enums, Futures

### 146. `@dataclass` rewrites `__init__`
Also generates `__repr__`, `__eq__`, and optionally `__hash__`.

---

### 147. Enums are classes
`Enum` members are singletons, not just constants.

---

### 148. `__future__` imports change syntax
E.g., `from __future__ import annotations` postpones annotation evaluation.

#### Recursion & Assertions

### 149. Python doesn’t tail-optimize recursion
Deep recursion will still hit `RecursionError`.

---

### 150. Assertions can be stripped
Run Python with `-O` and all `assert` statements disappear.

---

---

# 🧠 Advanced Python: AST Transforms & Obscure Protocols

Now we’re going into **expert-level Python metaprogramming**:  
- How to rewrite Python code itself (AST).  
- Hidden / obscure protocols Python supports that most never use.

---

## ⚡ AST (Abstract Syntax Tree) Transforms

### 151. `ast.parse` lets you parse Python source into a tree
```python
import ast
tree = ast.parse("x = 1 + 2")
print(ast.dump(tree, indent=2))
```

---

### 152. You can walk & rewrite ASTs
Transformations can replace operations before execution.

```python
class AddToMul(ast.NodeTransformer):
    def visit_BinOp(self, node):
        self.generic_visit(node)
        if isinstance(node.op, ast.Add):
            return ast.BinOp(node.left, ast.Mult(), node.right)
        return node

tree = ast.parse("1 + 2")
tree = AddToMul().visit(tree)
code = compile(tree, "<ast>", "exec")
exec(code)  # prints nothing, but 1+2 became 1*2
```

---

### 153. AST hooks can make custom “languages”
You can redefine operators, inject logging, or enforce rules at *compile time*.  
Frameworks like `astroid`, `pylint`, and `black` rely on this.

---

### 154. `compile` can run AST directly
Not just strings:

```python
import ast
expr = ast.parse("42", mode="eval")
print(eval(compile(expr, "<ast>", "eval")))  # 42
```

---

### 155. You can instrument code at import
Use `sys.meta_path` with an importer that parses source → rewrites AST → compiles → loads.  
That’s how tools like `coverage.py` and `Cython` inject themselves.

---

---

## 🔮 Obscure Protocols & Dunders

### 156. Buffer protocol
Objects can expose raw memory (`__buffer__` is internal in CPython).  
Exposed via `memoryview`.

```python
b = bytearray(b"abc")
m = memoryview(b)
print(m[0])  # 97
```

---

### 157. Awaitable protocol
Any object with `__await__` can be awaited.

```python
class Dummy:
    def __await__(self): yield 42

async def main():
    await Dummy()
```

---

### 158. Async iterator protocol
Objects with `__aiter__` and `__anext__` support `async for`.

```python
class A:
    def __aiter__(self): return self
    async def __anext__(self): raise StopAsyncIteration
```

---

### 159. Async context managers
Use `__aenter__` and `__aexit__`.

```python
class AsyncCtx:
    async def __aenter__(self): print("enter")
    async def __aexit__(self, *a): print("exit")
```

---

### 160. `__fspath__` protocol
Objects can define a filesystem path representation.  
`os.fspath(obj)` calls it.

```python
import os
class P:
    def __fspath__(self): return "/tmp/foo"
print(os.fspath(P()))  # "/tmp/foo"
```

---

### 161. `__missing__` in dict subclasses
Custom handler for missing keys (basis of `defaultdict`).

---

### 162. `__truediv__`, `__floordiv__`
Separate hooks for `/` and `//`.

---

### 163. `__enter__` / `__exit__`
Power normal context managers (`with`).

---

### 164. Exception chaining protocol
- `__cause__`: explicit `raise ... from ...`  
- `__context__`: implicit chaining  
- `__suppress_context__`: control display  

---

### 165. Pickling protocol
- `__reduce__` / `__reduce_ex__`  
- `__getnewargs__`, `__getstate__`, `__setstate__`  

---

### 166. Descriptor protocol
- `__get__`, `__set__`, `__delete__`  
- Data descriptors (with `__set__`) override instance dict.  
- Non-data descriptors (like methods) don’t.

---

### 167. Numeric protocols
- `__abs__` → `abs(obj)`  
- `__int__`, `__float__`, `__complex__`  
- `__round__`, `__trunc__`, `__floor__`, `__ceil__`  

---

### 168. Iterable unpacking protocol
Driven by `__iter__` + `__next__`. That’s how `*obj` in unpacking works.

---

### 169. `__iter__` vs `__getitem__`
If `__iter__` is missing, Python falls back to repeatedly calling `__getitem__` starting from 0.  
That’s why old classes without `__iter__` can still loop.

---

### 170. Boolean protocol
- `__bool__` → truthiness  
- Falls back to `__len__` if missing  

---

---

# 🚀 Next Levels
- **AST rewriting + import hooks**: build a system that rewrites Python code transparently.  
- **Custom protocols**: mixing `__class_getitem__`, `__init_subclass__`, and `__prepare__` for *framework-like magic*.  
- **Runtime metaprogramming**: modifying functions/classes after definition with decorators, descriptors, or AST transforms.  

---

Do you want me to **expand with concrete AST transform examples** (like auto-logging every function call, enforcing type checks, or rewriting operators), or keep building the catalog of *obscure dunder protocols*?  
# 🧠 Python Expert-Level Stuff You Should At Least Have Read About

Here’s a set of **deep Python metaprogramming and protocol tricks** that serious Python hackers bump into eventually.  
Even if you don’t use them daily, knowing they exist puts you ahead.

---

## ⚡ Metaprogramming & DSL Tricks

### 201. `exec` with custom globals/locals
You can sandbox or inject code at runtime.

```python
ns = {"x": 2}
exec("y = x * 5", ns)
print(ns["y"])  # 10
```

---

### 202. AST-based DSLs
Libraries like [MacroPy](https://github.com/lihaoyi/macropy) let you extend Python syntax itself using AST transforms.  
Example: pattern matching before Python had it natively.

---

### 203. Operator overloading DSLs
`numpy`, `sympy`, `pandas` all redefine operators (`+`, `@`, `>>`, `[]`) to build *expression trees* instead of doing real math.

```python
import sympy as sp
x = sp.Symbol("x")
print((x + 1)**2)  # builds symbolic tree, not a number
```

---

### 204. Metaclasses as registries
Auto-register subclasses on creation.

```python
class Registry(type):
    subclasses = []
    def __init__(cls, name, bases, ns):
        super().__init__(name, bases, ns)
        Registry.subclasses.append(cls)

class Base(metaclass=Registry): pass
class A(Base): pass
print(Registry.subclasses)  # [<class '__main__.Base'>, <class '__main__.A'>]
```

---

### 205. AST transformations at import
Using `importlib.abc.SourceLoader`, you can rewrite source before execution.  
That’s how tools like `coverage.py`, `coconut`, `numba` hook Python.

---

---

## 🔮 Obscure & Rare Protocols


### 207. `__aiter__` returning async generator
Instead of `self`, you can yield async values directly.

---

### 208. `__getstate__` / `__setstate__`
Custom pickling beyond `__reduce__`.

---

### 209. `__slots__` + `__weakref__`
If you use `__slots__`, you must explicitly add `__weakref__` if you want weakrefs.

---

### 210. `__call__` + decorators
Any callable class can act like a decorator:

```python
class Deco:
    def __call__(self, f):
        print("Decorating", f.__name__)
        return f

@Deco()
def f(): pass
```

---

### 211. Context managers from generators
`contextlib.contextmanager` turns a generator into a `with` block manager.

```python
from contextlib import contextmanager

@contextmanager
def temp():
    print("enter")
    yield
    print("exit")

with temp(): pass
```

---

### 212. `__or__` overloading in modern Python
PEP 584 lets `dict | dict` merge dictionaries.  
You can override `__or__` in your own classes too.

---

### 213. Protocols for pattern matching
Since Python 3.10, structural pattern matching (`match`) uses:  
- `__match_args__`  
- `__getitem__` (for mapping patterns)  
- `__iter__` (for sequence patterns)  

---

### 214. `__rshift__` (`>>`) used in DSLs
E.g., in SQLAlchemy / Luigi pipelines to chain tasks.

---

### 215. `__complex__` & `__bytes__`
Not common, but useful in numeric/data libs.

---

### 216. `__ipow__`, `__ilshift__`, etc.
In-place versions of operators: `**=`, `<<=`, etc.

---

### 217. PEP 443 — Single Dispatch
`functools.singledispatch` turns a function into a type-based dispatcher.

```python
from functools import singledispatch

@singledispatch
def f(x): print("default", x)

@f.register(int)
def _(x): print("int", x)

f(42)  # int 42
```

---

### 218. Multiple dispatch libraries
`multipledispatch` and `plum` extend this idea beyond single argument dispatch.

---

### 219. Protocol classes (`typing.Protocol`)
You can define “duck types” without inheritance.

```python
from typing import Protocol

class Flyer(Protocol):
    def fly(self) -> None: ...

def go(x: Flyer): x.fly()
```

---

### 220. `__future__` hacks
Some imports literally change Python syntax/semantics at parse time.  
- `from __future__ import division` → makes `/` true division.  
- `from __future__ import annotations` → stores hints as strings.  

---

---

## 🧠 “Read About” Areas
If you want to call yourself **expert**, you should at least know these topics exist:
- **Descriptors**: the root of properties, methods, staticmethod/classmethod.  
- **Metaclasses**: class factories with hooks (`__new__`, `__init__`, `__prepare__`).  
- **AST & importlib hooks**: rewriting Python before it runs.  
- **Data model protocols**: the full suite of dunders (`__iter__`, `__await__`, `__fspath__`, etc.).  
- **Pattern matching protocols**: `__match_args__`.  
- **Multiple dispatch**: single dispatch in stdlib, full multiple dispatch in 3rd party libs.  
- **Context managers**: sync and async, plus generator-based (`contextlib`).  
- **Pickling protocol**: `__reduce__`, `__getnewargs__`, `__setstate__`.  
- **Typing extensions**: `Protocol`, `Literal`, `TypedDict`, `Annotated`.  

---

## ✅ Where To Go Next
- Expand with concrete AST transformation projects (auto-logging, runtime type checks, embedded DSLs).
- Or add a systematic map of protocols grouped by category (numeric, container, async, context, import, pickling).

---

# 🧩 Expert Additions (Deep, Often Overlooked)

### 221. The GIL and CPU-bound code
CPython has a Global Interpreter Lock. Threads don’t run Python bytecode in true parallel on one process. Use multiprocessing, C extensions, or vectorized libs for CPU-bound work; threads shine for I/O.

---

### 222. “Atomic” operations aren’t a language guarantee
Some operations (e.g., `list.append`) are thread-safe in CPython due to the GIL, but this is not a cross-implementation guarantee nor a substitute for proper locking around invariants.

---

### 223. Instance dicts use key-sharing
CPython stores instance attributes with a shared key table per class ("split dicts"). Many instances of the same class have compact attribute storage, improving memory and cache locality.

---

### 224. Dicts are compact, ordered, and amortized O(1)
CPython dicts maintain insertion order with a compact index/entry layout. Deletes leave tombstones until compaction during resize; iteration is safe against mutations only if you don’t change the dict size.

---

### 225. Functions are descriptors; bound methods carry `__self__`
Accessing `A.f` gives the function; accessing `A().f` produces a bound method whose `__self__` is the instance. That binding is just the function’s descriptor protocol in action.

---

### 226. Attribute lookup precedence with descriptors
Data descriptors (`__get__` + `__set__`) win over instance `__dict__`; non-data descriptors can be shadowed by instance attributes. This ordering explains why properties override instance attributes but methods can be replaced per-instance.

---

### 227. Python 3.11+ has an adaptive interpreter
CPython specializes opcodes at runtime (“inline caching”). After a warmup, attribute access and calls can speed up without code changes. Inspect with `dis` to see specialized opcodes.

---

### 228. Locals are faster than globals; bind methods locally
Name resolution hits locals first; attribute lookups are slower than local variables. A classic micro-opt: `append = lst.append; 
for x in it: append(x)` avoids repeated attribute lookups.

---

### 229. List multiplication aliases inner lists
`grid = [[0]*3]*3` shares the same inner list three times. Mutating one row mutates all. Use a comprehension: `[[0 for _ in range(3)] for _ in range(3)]`.

---

### 230. Buffer protocol and `memoryview`
`memoryview` exposes zero-copy slices/views over bytes-like objects (e.g., `bytearray`, `array`, NumPy). Great for binary I/O and interop without allocations.

---

### 231. Path protocol: `__fspath__` and `os.fspath`
Objects can be path-like by implementing `__fspath__`. Most stdlib functions call `os.fspath(obj)` to get a string/path.

---

### 232. `ContextVar` beats thread-locals for async
`contextvars` propagate across `await` boundaries; thread-locals don’t map to tasks. Use `ContextVar` for request-scoped state in asyncio code.

---

### 233. Async cancellation and shielding
`asyncio.CancelledError` unwinds coroutines; use `asyncio.shield(task)` to protect a subtask. In 3.11+, `asyncio.TaskGroup` structures task lifetimes and cancellation semantics.

---

### 234. Signal handling: main thread only
Python delivers signals to the main thread. On Unix, integrate with asyncio via `loop.add_signal_handler`. Windows support is limited for some signals.

---

### 235. PEP 420: Namespace packages
Packages can exist without `__init__.py` and be spread across multiple directories. The import system merges them at runtime.

---

### 236. Import caching and circular imports
Modules are inserted into `sys.modules` before execution completes. In circular imports you can observe half-initialized modules; import inside functions or refactor to avoid this.

---

### 237. `__prepare__` customizes the class body namespace
Metaclasses can control the mapping used during class creation (e.g., ordered mapping, validation). This runs before the class body executes.

---

### 238. `__class_getitem__` for generics
Classes can define subscription syntax without `typing`, e.g., `Box[int]`. Useful for runtime registries or DSLs.

---

### 239. `__init_subclass__` is a subclass hook
The base class can validate or auto-register subclasses at definition time without a metaclass.

---

### 240. F-strings: `=` debug spec and conversions
`f"{x=}"` prints both the name and value; `!r/!s/!a` choose representation; format spec follows `:` as in `f"{n:=#10x}"`.

---

### 241. `copy` protocol and customizations
Shallow vs deep copy via `copy.copy`/`copy.deepcopy`. Classes can implement `__copy__` and `__deepcopy__` to control behavior and memoization.

---

### 242. Set iteration order is hash-dependent
Unlike dicts, sets do not promise stable iteration order across processes/runs. Hash randomization (`PYTHONHASHSEED`) affects iteration order.

---

# 😼 Looks Easy, Actually Tricky (Common Pitfalls)

### 243. Late-binding closures in loops
Lambdas/functions capture variables, not their values. Bind via a default.

```python
funcs = [lambda: i for i in range(3)]
print([f() for f in funcs])  # [2, 2, 2]

funcs = [lambda i=i: i for i in range(3)]
print([f() for f in funcs])  # [0, 1, 2]
```

---

### 244. Mutable class attributes are shared
Put mutables on the instance, not the class.

```python
class A:
    items = []  # shared by all instances!

a, b = A(), A()
a.items.append(1)
print(b.items)  # [1]

class B:
    def __init__(self): self.items = []
```

---

### 245. Dataclass mutable defaults
Use `default_factory`, not a mutable default.

```python
from dataclasses import dataclass, field

@dataclass
class Bag:
    xs: list[int] = field(default_factory=list)
```

---

### 246. Don’t use `is` for value equality
`is` is identity. Only use it for singletons like `None`.

```python
name = "hi"
print(name is "hi")   # implementation detail; don’t rely on it
print(name == "hi")   # correct
print(x is None)       # correct None check
```

---

### 247. `and`/`or` return operands, not bools
They short-circuit and return the last evaluated operand.

```python
print(0 or 5)        # 5
print("" or "x")    # 'x'
print([] and 7)      # []
# Beware when chaining with non-bool values.
```

---

### 248. Don’t mutate while iterating
Iterating and changing size leads to skipped items or `RuntimeError`.

```python
xs = [1,2,3,4]
for x in xs:          # BAD
    if x % 2 == 0:
        xs.remove(x)
print(xs)             # [1, 3] (worked by accident here)

xs = [1,2,3,4]        # GOOD
xs = [x for x in xs if x % 2]
```

---

### 249. Slicing is a copy (lists/tuples), not a view
Modifying the slice result won’t affect the original; NumPy differs.

```python
xs = [1,2,3]; ys = xs[:]
ys[0] = 9
print(xs[0])  # 1
```

---

### 250. Objects used as dict/set keys must be immutable w.r.t. hashing
If `__eq__`/`__hash__` depend on fields you mutate later, lookups break.

```python
class Key:
    def __init__(self, v): self.v = v
    def __hash__(self): return hash(self.v)
    def __eq__(self, o): return self.v == o.v

k = Key(1); d = {k: "x"}
k.v = 2  # now d can't find the bucket for k
```

---

### 251. `sum()` on strings/lists is slow or wrong
Use `"".join(parts)` for strings, `itertools.chain` or comprehensions for lists.

```python
# Strings
"".join(["a", "b", "c"])  # fast

# Lists
from itertools import chain
list(chain.from_iterable(list_of_lists))
```

---

### 252. Bytes vs str
`str` ↔ `bytes` conversions are explicit; always declare encodings.

```python
data = "π".encode("utf-8")   # bytes
text = data.decode("utf-8")  # str
```

---

### 253. Raw strings for regex and Windows paths
Avoid accidental escapes with `r"..."`.

```python
import re
re.compile(r"\d+\\path")  # raw avoids double-escaping
```

---

### 254. Naive vs aware datetimes
Always use timezone-aware datetimes for comparisons/storage.

```python
from datetime import datetime, timezone
now = datetime.now(timezone.utc)
```

---

### 255. Asyncio: avoid blocking calls
`time.sleep`, blocking I/O, and CPU work stall the loop.

```python
# BAD in async code
# time.sleep(1)
# GOOD
import asyncio
await asyncio.sleep(1)
```

---

### 256. Logger formatting: don’t preformat
Let logging do deferred formatting; it’s faster and avoids work when disabled.

```python
logger.info("User %s logged in", user_id)  # good
# logger.info(f"User {user_id} logged in")  # eager formatting
```

---

### 257. Exception handling order and re-raise
Catch specific exceptions first; use bare `raise` to keep traceback or `raise ... from ...` to chain.

```python
try:
    risky()
except ValueError as e:
    raise  # preserves original traceback
```

---

### 258. Always close resources
Prefer context managers to ensure cleanup.

```python
with open("data.txt", encoding="utf-8") as f:
    for line in f: ...
```

---

### 259. Shallow vs deep copy
Nested structures need `deepcopy`.

```python
import copy
a = [[1],[2]]
b = copy.copy(a)       # shares inner lists
c = copy.deepcopy(a)   # full copy
```

---

### 260. CSV newline/encoding pitfalls
On Windows, pass `newline=""`; always set `encoding`.

```python
import csv
with open("out.csv", "w", newline="", encoding="utf-8") as f:
    w = csv.writer(f)
    w.writerow(["a", "b"])
```

---

### 261. Shadowing stdlib names
Don’t name your module `json.py`, `random.py`, etc., or imports will resolve to your file.

---

### 262. Relative imports can surprise
Prefer absolute imports inside packages to avoid ambiguity and circular import headaches.

Next: [Overview](../go/index.md)
