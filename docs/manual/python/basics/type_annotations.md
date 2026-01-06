# Type Annotations in Python

Type annotations allow you to indicate the expected data types of variables, function arguments, and return values. They help with code readability and enable better tooling and static analysis.

## Basic Usage

```python
def add(x: int, y: int) -> int:
    return x + y

name: str = "Alice"
age: int = 30
```

## Common Types
- `int`, `float`, `str`, `bool`
- `List`, `Dict`, `Tuple`, `Set` (from `typing` module)

## Example with Collections
```python
from typing import List, Dict

def process_items(items: List[str]) -> Dict[str, int]:
    return {item: len(item) for item in items}
```

## Optional and Union
```python
from typing import Optional, Union

def get_name(user: dict) -> Optional[str]:
    return user.get("name")

def parse_value(val: Union[int, str]) -> str:
    return str(val)
```

## Benefits
- Better editor support
- Early error detection
- Improved documentation

## Resources
- [PEP 484](https://peps.python.org/pep-0484/)
- [Python typing docs](https://docs.python.org/3/library/typing.html) 

---

# Advanced / Expert Type Hints

The following patterns cover powerful typing features used in larger codebases and libraries. Examples note versions where features landed; use `typing_extensions` for backports when needed.

## Variance and Generics

```python
from typing import Generic, TypeVar, Sequence

T_co = TypeVar("T_co", covariant=True)   # can be substituted by a subtype
T_contra = TypeVar("T_contra", contravariant=True)  # by a supertype

class Reader(Generic[T_co]):
    def read(self) -> T_co: ...

class Writer(Generic[T_contra]):
    def write(self, item: T_contra) -> None: ...

# Variance in stdlib: Sequence is covariant, list is invariant
def takes_strings(xs: Sequence[str]) -> None: ...
strings: list[str] = ["a"]
# takes_strings(strings)  # OK: list[str] is a Sequence[str]
```

## Protocols (Structural Subtyping)

```python
from typing import Protocol, runtime_checkable

class FileLike(Protocol):
    def read(self, n: int = ...) -> bytes: ...
    def write(self, data: bytes) -> int: ...

@runtime_checkable
class SupportsClose(Protocol):
    def close(self) -> None: ...

def process(f: FileLike) -> None:
    chunk = f.read(1024)
    f.write(chunk)
```

Protocols let any object “duck type” to the interface without inheritance.

## Self Type (PEP 673)

```python
from typing import Self

class Builder:
    def set_x(self, v: int) -> Self:
        self.x = v
        return self

    @classmethod
    def new(cls) -> Self:
        return cls()
```

## Overloads and Literal-Directed APIs

```python
from typing import overload, Literal

@overload
def parse_flag(s: Literal["on", "true", "1"]) -> bool: ...

@overload
def parse_flag(s: Literal["off", "false", "0"]) -> bool: ...

@overload
def parse_flag(s: str) -> None: ...

def parse_flag(s: str) -> bool | None:
    s = s.lower()
    if s in {"on", "true", "1"}: return True
    if s in {"off", "false", "0"}: return False
    return None
```

## Higher-Order Functions with ParamSpec and Concatenate (PEP 612)

```python
from typing import Callable, ParamSpec, TypeVar, Concatenate

P = ParamSpec("P")
R = TypeVar("R")

def with_logging(func: Callable[P, R]) -> Callable[P, R]:
    def wrapper(*args: P.args, **kwargs: P.kwargs) -> R:
        print("calling", func.__name__)
        return func(*args, **kwargs)
    return wrapper

T = TypeVar("T")
def bind_first(fn: Callable[Concatenate[T, P], R], /, first: T) -> Callable[P, R]:
    def wrapped(*args: P.args, **kw: P.kwargs) -> R:
        return fn(first, *args, **kw)
    return wrapped
```

## Variadic Generics with TypeVarTuple (PEP 646)

```python
from typing import TypeVarTuple, Unpack, Tuple

Ts = TypeVarTuple("Ts")

def head_tail[*Ts](t: tuple[*Ts]) -> tuple[tuple[()]] | tuple[tuple[Unpack[Ts]]]:
    # Example signature showing variadic tuples; use in libraries that model shapes.
    return t[:1], t[1:]

def prepend_int[*Ts](t: tuple[*Ts]) -> tuple[int, *Ts]:
    return (0, *t)
```

Note: Use `typing_extensions` for older Python versions.

## TypedDict (PEP 589) with Required/NotRequired (PEP 655)

```python
from typing import TypedDict, Required, NotRequired

class User(TypedDict, total=False):
    id: Required[int]        # must be present
    name: str               # optional because total=False
    email: NotRequired[str] # explicitly optional
```

## Final, ClassVar, NewType, TypeAlias

```python
from typing import Final, ClassVar, NewType, TypeAlias

API_KEY: Final = "dont-mutate"

class Config:
    cache: ClassVar[dict[str, str]] = {}

UserId = NewType("UserId", int)
PathLike: TypeAlias = str | bytes
```

## Annotated Metadata (PEP 593)

```python
from typing import Annotated

class Range:
    def __init__(self, lo: int, hi: int):
        self.lo, self.hi = lo, hi

Port = Annotated[int, Range(1, 65535)]

def connect(port: Port) -> None: ...  # tooling can read Range metadata
```

## Async Typing Primitives

```python
from typing import Awaitable, Coroutine, AsyncIterator, AsyncContextManager

async def fetch() -> str: ...

def start() -> Awaitable[str]:
    return fetch()

async def gen() -> AsyncIterator[int]:
    for i in range(3):
        yield i

async def use_cm(cm: AsyncContextManager[str]) -> None:
    async with cm as s:
        print(s)
```

## Narrowing and Exhaustiveness with Never

```python
from typing import Never

def assert_never(x: Never) -> Never:
    raise AssertionError(f"Unhandled: {x!r}")

def handle(event: tuple[str, object]) -> None:
    kind, payload = event
    if kind == "open":
        ...
    elif kind == "close":
        ...
    else:
        assert_never(kind)  # type checkers flag non-exhaustive paths
```

## Generic Classes (Classic and PEP 695)

Classic:
```python
from typing import Generic, TypeVar
T = TypeVar("T")

class Box(Generic[T]):
    def __init__(self, value: T):
        self.value = value
```

PEP 695 (Python 3.12+):
```python
class Box[T]:
    def __init__(self, value: T):
        self.value = value
```

## Override Annotations

```python
try:
    from typing import override  # Python 3.12+
except ImportError:  # pragma: no cover
    from typing_extensions import override

class Base:
    def f(self) -> int: ...

class Child(Base):
    @override
    def f(self) -> int:
        return 1
```

---

## When To Use What (Cheatsheet)

- Protocol: define behavior by interface, not inheritance. Use for pluggable backends and I/O abstractions. See also: `advanced/descriptors.md`, `advanced/type_system.md`.
- TypedDict: shape-check JSON/config dicts with optional/required keys. Use at module boundaries. Pairs well with `dataclasses` for internal models.
- ParamSpec/Concatenate: typing decorators and wrappers that forward arbitrary call signatures. See also: `decorators/decorators.md` and `advanced/decorators.md`.
- TypeVar/Generics: reusable containers/APIs that preserve element types (e.g., `Box[T]`, repositories). Prefer invariant generics unless variance is required.
- TypeVarTuple/Unpack: model variable-length tuple/list “shapes” (matrix dims, tensor shapes). Useful in numeric/array libraries.
- overload + Literal: APIs whose return type depends on a flag or literal mode (parse modes, binary/text). Keep implementation single and typed with union.
- Self: fluent builders and classmethods that return the most-derived type. Great for subclass-friendly APIs.
- Annotated: attach validation/metadata for frameworks and tooling (FastAPI, pydantic, CLI schemas). Keep runtime consumers documented.
- Final/ClassVar/NewType: constants and IDs (`UserId`), and class-level caches; prevent accidental mutation or misuse.
- Async types (Awaitable/AsyncIterator): document async streaming vs. task-returning functions. See also: `async.md` and `advanced/custom_awaitables.md`.

Cross-refs to examples
- Decorator typing with forwarding: `advanced/decorators.md:1`
- Protocol-based design: `advanced/type_system.md:1`, `advanced/descriptors.md:1`
- Async generator typing and streams: `async.md:1`
- Datamodel interactions (descriptors/protocols): `classes/descriptors.md:1`, `advanced/descriptors.md:1`

Next: [Virtualenv & Pip](venv_pip.md)
