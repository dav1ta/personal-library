# Python Functions

## Introduction

Functions are fundamental building blocks in Python programming. This guide covers various aspects of functions, from basic usage to advanced features.

## Basic Functions

### Function Definition
```python
def greet(name: str) -> str:
    """Return a greeting message."""
    return f"Hello, {name}!"
```

### Function Parameters
```python
# Positional arguments
def add(x: int, y: int) -> int:
    return x + y

# Keyword arguments
def greet(name: str, greeting: str = "Hello") -> str:
    return f"{greeting}, {name}!"

# Variable arguments
def sum_all(*args: int) -> int:
    return sum(args)

# Keyword variable arguments
def print_info(**kwargs: str) -> None:
    for key, value in kwargs.items():
        print(f"{key}: {value}")
```

## Advanced Function Features

### Lambda Functions
```python
# Basic lambda
square = lambda x: x ** 2

# Lambda with closure
x = 2
f = lambda y: x * y
x = 3
g = lambda y: x * y
print(f(10))  # 30
print(g(10))  # 30
```

### Inner Functions
```python
def outer(x: int) -> int:
    def inner(y: int) -> int:
        nonlocal x  # Access outer function's variable
        x += y
        return x
    return inner

# Usage
counter = outer(0)
print(counter(1))  # 1
print(counter(2))  # 3
```

### Recursion
```python
import sys

# Get current recursion limit
print(sys.getrecursionlimit())  # Default is 1000

# Set recursion limit
sys.setrecursionlimit(2000)

# Recursive function
def factorial(n: int) -> int:
    if n <= 1:
        return 1
    return n * factorial(n - 1)
```

## Function Inspection

### Basic Attributes
```python
def example(x: int) -> int:
    """Example function for inspection."""
    return x * 2

# Function attributes
print(example.__name__)  # Function name
print(example.__qualname__)  # Fully qualified name
print(example.__module__)  # Module name
print(example.__doc__)  # Documentation string
print(example.__annotations__)  # Type hints
```

### Advanced Inspection
```python
import inspect

def func(x: int, y: float, debug: bool = False) -> float:
    pass

# Get function signature
sig = inspect.signature(func)
print(sig)  # (x: int, y: float, debug: bool = False) -> float

# Compare function signatures
def func1(x: int) -> int: pass
def func2(x: int) -> int: pass
assert inspect.signature(func1) == inspect.signature(func2)
```

### Frame Inspection
```python
def spam(x: int, y: int) -> None:
    z = x + y
    grok(z)

def grok(a: int) -> None:
    b = a * 10
    # Get current frame's local variables
    print(inspect.currentframe().f_locals)  # {'a': 5, 'b': 50}

# Frame attributes
f = inspect.currentframe()
print(f.f_back)  # Previous stack frame
print(f.f_code)  # Code object
print(f.f_locals)  # Local variables
print(f.f_globals)  # Global variables
print(f.f_builtins)  # Built-in names
print(f.f_lineno)  # Line number
print(f.f_lasti)  # Current instruction
print(f.f_trace)  # Trace function
```

## Best Practices

1. Use type hints for better code clarity
2. Write descriptive docstrings
3. Keep functions focused and small
4. Use meaningful parameter names
5. Consider using `*args` and `**kwargs` for flexibility
6. Be careful with mutable default arguments
7. Use recursion judiciously
8. Document function behavior and side effects

- [Decorators](decorators.md)
- Type Annotations
- [Modules](modules.md)
- Testing 

Next: [Modules](modules.md)
