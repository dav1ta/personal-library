# Python Language Structure

## Introduction

This guide covers the fundamental structure and features of Python, including literals, operations, and exception handling.

## Literals

Literals are fixed values in Python code.

### Numeric Literals
```python
# Integer literals
42              # Decimal
0b101010        # Binary
0o52            # Octal
0x2a            # Hexadecimal

# Numeric separators
123_456_789     # Decimal with separators
0x1234_5678     # Hexadecimal with separators
0b111_00_101    # Binary with separators
123.789_012     # Float with separators
```

### String Literals
```python
# String literals
'Hello'         # Single quotes
"World"         # Double quotes
'''Multi-line
string'''       # Triple quotes
```

## Operations

### Iterable Operations
```python
# Basic iteration
for item in sequence:
    print(item)

# Variable unpacking
a, b, c = [1, 2, 3]
a, *rest = [1, 2, 3, 4]  # a=1, rest=[2,3,4]
a, _, c = [1, 2, 3]      # a=1, c=3

# Membership testing
x in sequence
x not in sequence

# Expansion
[a, *sequence, b]        # List
(a, *sequence, b)        # Tuple
{a, *sequence, b}        # Set
```

### Set Operations
```python
# Set creation and operations
s1 = {'a', 'b', 'c'}
s2 = {'b', 'c', 'd'}

# Union
s1 | s2                  # {'a', 'b', 'c', 'd'}
s1.union(s2)

# Intersection
s1 & s2                  # {'b', 'c'}
s1.intersection(s2)

# Difference
s1 - s2                  # {'a'}
s1.difference(s2)

# Symmetric difference
s1 ^ s2                  # {'a', 'd'}
s1.symmetric_difference(s2)

# Set methods
s1.add('d')
s1.remove('a')           # Raises KeyError if not found
s1.discard('a')          # No error if not found
s1.pop()                 # Removes and returns arbitrary element
s1.clear()               # Removes all elements
```

### Dictionary Operations
```python
# Dictionary operations
d = {'a': 1, 'b': 2}

# Access
value = d['a']
value = d.get('a', 0)    # With default value

# Modification
d['c'] = 3
d.update({'d': 4})
del d['a']

# Keys and values
keys = d.keys()
values = d.values()
items = d.items()

# Dictionary comprehension
d = {k: v for k, v in zip(keys, values)}
```

### List Operations
```python
# List comprehension
[expression for item in iterable if condition]

# Nested comprehension
[expression for item1 in iterable1 if condition1
            for item2 in iterable2 if condition2]

# Generator expression
(x*x for x in range(10))

# List methods
lst.append(x)
lst.extend(iterable)
lst.insert(i, x)
lst.remove(x)
lst.pop(i)
lst.clear()
lst.index(x)
lst.count(x)
lst.sort()
lst.reverse()
```

## Iteration Tools

### Enumerate
```python
# Basic enumeration
for i, item in enumerate(sequence):
    print(i, item)

# Custom start
for i, item in enumerate(sequence, start=1):
    print(i, item)
```

### Zip
```python
# Basic zip
for x, y in zip(sequence1, sequence2):
    print(x, y)

# Unzipping
pairs = [(1, 'a'), (2, 'b'), (3, 'c')]
numbers, letters = zip(*pairs)
```

## Exception Handling

### Exception Hierarchy
```python
BaseException
├── SystemExit
├── KeyboardInterrupt
├── GeneratorExit
└── Exception
    ├── StopIteration
    ├── ArithmeticError
    ├── AssertionError
    ├── AttributeError
    ├── BufferError
    ├── EOFError
    ├── ImportError
    ├── LookupError
    ├── MemoryError
    ├── NameError
    ├── OSError
    ├── ReferenceError
    ├── RuntimeError
    ├── SyntaxError
    ├── SystemError
    ├── TypeError
    ├── ValueError
    └── Warning
```

### Custom Exceptions
```python
class CustomError(Exception):
    """Base class for custom exceptions."""
    pass

class ValidationError(CustomError):
    """Raised when validation fails."""
    pass

# Usage
try:
    raise ValidationError("Invalid input")
except ValidationError as e:
    print(f"Error: {e}")
```

### Exception Chaining
```python
try:
    # Some code that may raise an exception
    raise ValueError("Invalid value")
except ValueError as e:
    # Chain the exception
    raise RuntimeError("Failed to process") from e
```

### Exception Attributes
```python
try:
    raise ValueError("Invalid value")
except ValueError as e:
    print(e.args)        # Tuple of arguments
    print(e.__cause__)   # Original exception
    print(e.__context__) # Exception context
```

## Best Practices

1. Use appropriate literals
2. Choose the right data structure
3. Use list comprehensions for simple transformations
4. Use generator expressions for large datasets
5. Handle exceptions appropriately
6. Use custom exceptions for domain-specific errors
7. Document exception handling
8. Follow PEP 8 style guide

## Common Pitfalls

1. Modifying collections during iteration
2. Using mutable default arguments
3. Ignoring exceptions
4. Catching too broad exceptions
5. Not using context managers
6. Forgetting to close resources
7. Using global variables
8. Not handling edge cases

- [Data Types](data_types.md)
- Functions
- Error Handling
- Style Guide 

Next: [Collections](collections.md)
