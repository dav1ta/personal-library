# Python Data Model

This guide covers Python's data model, special methods, and how they enable object behavior customization.

## Table of Contents
- [Introduction](#introduction)
- [Special Methods](#special-methods)
- [Object Lifecycle](#object-lifecycle)
- [Attribute Access](#attribute-access)
- [Container Types](#container-types)
- [Numeric Types](#numeric-types)
- [Context Managers](#context-managers)
- [Best Practices](#best-practices)

## Introduction

### What is the Data Model?
The Python data model defines how objects behave in response to language operations. Special methods (dunder methods) allow objects to implement and customize these behaviors.

### Key Concepts
- Special methods (dunder methods)
- Object protocols
- Language integration
- Custom behavior

## Special Methods

### Basic Object Operations
```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return f'Vector({self.x}, {self.y})'

    def __str__(self):
        return f'({self.x}, {self.y})'

    def __abs__(self):
        return (self.x ** 2 + self.y ** 2) ** 0.5

    def __bool__(self):
        return bool(abs(self))
```

### Container Operations
```python
class FrenchDeck:
    ranks = [str(n) for n in range(2, 11)] + list('JQKA')
    suits = 'spades diamonds clubs hearts'.split()
    
    def __init__(self):
        self._cards = [Card(rank, suit) 
                      for suit in self.suits 
                      for rank in self.ranks]
        
    def __len__(self):
        return len(self._cards)
        
    def __getitem__(self, position):
        return self._cards[position]
```

## Object Lifecycle

### Creation and Destruction
```python
class Resource:
    def __new__(cls, *args, **kwargs):
        instance = super().__new__(cls)
        print("Creating new instance")
        return instance

    def __init__(self, name):
        self.name = name
        print(f"Initializing {name}")

    def __del__(self):
        print(f"Cleaning up {self.name}")
```

### Context Management
```python
class DatabaseConnection:
    def __enter__(self):
        print("Opening connection")
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        print("Closing connection")
        if exc_type:
            print(f"Error occurred: {exc_val}")
            return False
        return True
```

## Attribute Access

### Basic Attribute Access
```python
class LazyProperty:
    def __init__(self, function):
        self.function = function
        self.name = function.__name__

    def __get__(self, obj, type=None):
        if obj is None:
            return self
        value = self.function(obj)
        setattr(obj, self.name, value)
        return value
```

### Custom Attribute Access
```python
class ValidatedProperty:
    def __init__(self, name):
        self.name = name

    def __get__(self, instance, owner):
        if instance is None:
            return self
        return instance.__dict__[self.name]

    def __set__(self, instance, value):
        if not isinstance(value, (int, float)):
            raise TypeError("Value must be numeric")
        instance.__dict__[self.name] = value
```

## Container Types

### Sequence Types
```python
class Sequence:
    def __init__(self, items):
        self._items = list(items)

    def __len__(self):
        return len(self._items)

    def __getitem__(self, index):
        return self._items[index]

    def __setitem__(self, index, value):
        self._items[index] = value

    def __delitem__(self, index):
        del self._items[index]
```

### Mapping Types
```python
class DictLike:
    def __init__(self):
        self._data = {}

    def __getitem__(self, key):
        return self._data[key]

    def __setitem__(self, key, value):
        self._data[key] = value

    def __delitem__(self, key):
        del self._data[key]

    def __contains__(self, key):
        return key in self._data
```

## Numeric Types

### Basic Numeric Operations
```python
class Number:
    def __init__(self, value):
        self.value = value

    def __add__(self, other):
        return Number(self.value + other.value)

    def __sub__(self, other):
        return Number(self.value - other.value)

    def __mul__(self, other):
        return Number(self.value * other.value)

    def __truediv__(self, other):
        return Number(self.value / other.value)
```

### Comparison Operations
```python
class Comparable:
    def __init__(self, value):
        self.value = value

    def __eq__(self, other):
        return self.value == other.value

    def __lt__(self, other):
        return self.value < other.value

    def __le__(self, other):
        return self.value <= other.value
```

## Context Managers

### Synchronous Context Managers
```python
class FileManager:
    def __init__(self, filename, mode):
        self.filename = filename
        self.mode = mode
        self.file = None

    def __enter__(self):
        self.file = open(self.filename, self.mode)
        return self.file

    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.file:
            self.file.close()
```

### Asynchronous Context Managers
```python
class AsyncResource:
    async def __aenter__(self):
        print("Acquiring resource")
        return self

    async def __aexit__(self, exc_type, exc_val, exc_tb):
        print("Releasing resource")
        if exc_type:
            print(f"Error occurred: {exc_val}")
            return False
        return True
```

## Best Practices

1. **Special Method Usage**
   - Use special methods for language integration
   - Follow Python's conventions
   - Document behavior clearly

2. **Performance Considerations**
   - Optimize critical operations
   - Use appropriate data structures
   - Consider memory usage

3. **Error Handling**
   - Raise appropriate exceptions
   - Provide clear error messages
   - Handle edge cases

4. **Code Organization**
   - Group related special methods
   - Maintain consistent style
   - Use type hints

- [Classes](../advanced/classes.md)
- [Descriptors](../advanced/descriptors.md)
- [Iterators](../advanced/iterators.md)
- Metaclasses 