# Python Type System

This guide covers Python's type system, focusing on advanced typing features, best practices, and modern type annotations.

## Table of Contents
- [Introduction](#introduction)
- [Basic Types](#basic-types)
- [Advanced Types](#advanced-types)
- [Type Variables and Generics](#type-variables-and-generics)
- [Special Types](#special-types)
- [Type Checking](#type-checking)
- [Best Practices](#best-practices)

## Introduction

### What's New in Python 3.13
- Enhanced type system features
- Improved type inference
- New typing tools and utilities

### Type Checking Tools
- mypy: Static type checker
- pyright: Fast type checker
- pytype: Google's type checker
- pyre: Facebook's type checker

## Basic Types

### Primitive Types
```python
def process_data(
    text: str,
    number: int,
    decimal: float,
    flag: bool
) -> None:
    pass
```

### Collection Types
```python
from typing import List, Dict, Set, Tuple

def process_collections(
    items: List[int],
    mapping: Dict[str, int],
    unique: Set[str],
    pair: Tuple[int, str]
) -> None:
    pass
```

### Optional and Union Types
```python
from typing import Optional, Union

# Optional type (T | None)
def fetch_data(id: int) -> Optional[dict]:
    return None

# Union type (T | U)
def process_data(data: str | int) -> None:
    pass
```

## Advanced Types

### ParamSpec and Variadic Types
```python
from typing import Callable, TypeVar, ParamSpec

P = ParamSpec("P")
R = TypeVar("R")

def wrapper(func: Callable[P, R]) -> Callable[P, R]:
    def inner(*args: P.args, **kwargs: P.kwargs) -> R:
        return func(*args, **kwargs)
    return inner
```

### Variadic Generics
```python
from typing import Generic
from typing_extensions import TypeVarTuple, Unpack

Ts = TypeVarTuple('Ts')

class Array(Generic[Unpack[Ts]]):
    def __init__(self, *values: Unpack[Ts]) -> None:
        self.values = values
```

### Self Type
```python
from typing import Self

class Builder:
    def set_property(self, value: int) -> Self:
        self.value = value
        return self
```

## Type Variables and Generics

### Basic Generics
```python
from typing import Generic, TypeVar

T = TypeVar('T')

class Container(Generic[T]):
    def __init__(self, value: T) -> None:
        self.value = value

    def get_value(self) -> T:
        return self.value
```

### Bounded Type Variables
```python
from typing import TypeVar, Sequence

T = TypeVar('T', bound=Sequence)

def first_element(seq: T) -> T:
    return seq[0]
```

### Multiple Type Variables
```python
from typing import TypeVar, Generic

K = TypeVar('K')
V = TypeVar('V')

class KeyValuePair(Generic[K, V]):
    def __init__(self, key: K, value: V) -> None:
        self.key = key
        self.value = value
```

## Special Types

### TypedDict
```python
from typing import TypedDict

class Movie(TypedDict, total=False):
    title: str
    year: int
    director: str
```

### Protocol Classes
```python
from typing import Protocol

class Drawable(Protocol):
    def draw(self) -> None: ...
    def erase(self) -> None: ...
```

### Literal Types
```python
from typing import Literal

def process_status(status: Literal["success", "error", "pending"]) -> None:
    pass
```

## Type Checking

### Type Guards
```python
from typing import TypeGuard

def is_string_list(val: list[object]) -> TypeGuard[list[str]]:
    return all(isinstance(x, str) for x in val)
```

### Overload
```python
from typing import overload

@overload
def process(x: int) -> str: ...

@overload
def process(x: str) -> int: ...

def process(x: int | str) -> int | str:
    if isinstance(x, int):
        return str(x)
    return int(x)
```

### Type Aliases
```python
from typing import TypeAlias

Coordinates: TypeAlias = tuple[float, float]
Matrix: TypeAlias = list[list[float]]
```

## Best Practices

1. **Type Annotations**
   - Use type hints consistently
   - Avoid using `Any` when possible
   - Prefer `object` over `Any` for type safety

2. **Generic Types**
   - Use type variables for generic code
   - Specify bounds when appropriate
   - Document type constraints

3. **Error Handling**
   - Use `TypeGuard` for runtime type checking
   - Handle type errors gracefully
   - Provide clear error messages

4. **Code Organization**
   - Group related type definitions
   - Use type aliases for complex types
   - Document type relationships

- [Data Model](../advanced/data_model.md)
- [Decorators](../advanced/decorators.md)
- Metaclasses
- Performance Optimization 

Next: [Memory](memory.md)
