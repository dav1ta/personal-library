# Python Objects and Protocols

## Introduction

Python is an object-oriented language where everything is an object. This guide covers the fundamental concepts of Python objects, their behavior, and the protocols they implement.

## Objects in Python

### Object Characteristics
Every Python object has:
- Identity: A unique identifier (memory address)
- Type: Defines the object's behavior and supported operations
- Value: The data the object contains

```python
# Object creation and identity
a = 42
print(id(a))          # Object's identity
print(type(a))        # Object's type
print(a)              # Object's value
```

### First-Class Objects
In Python, all objects are first-class, meaning they can be:
- Assigned to variables
- Passed as arguments
- Returned from functions
- Compared with other objects
- Stored in data structures

```python
# First-class object examples
def process(obj):
    return obj

# Assign to variable
x = 42

# Pass as argument
result = process(x)

# Return from function
def create_object():
    return [1, 2, 3]

# Compare objects
a = [1, 2, 3]
b = [1, 2, 3]
print(a == b)  # True
print(a is b)  # False
```

## Memory Management

### Reference Counting
Python uses reference counting for memory management:
- Each object has a reference count
- Count increases when object is referenced
- Count decreases when reference is removed
- Object is deleted when count reaches zero

```python
import sys

# Reference counting
a = [1, 2, 3]
print(sys.getrefcount(a))  # Get reference count

b = a
print(sys.getrefcount(a))  # Count increased

del b
print(sys.getrefcount(a))  # Count decreased
```

### Garbage Collection
- Automatic cleanup of unreferenced objects
- Cyclic garbage collector for circular references
- Manual control via `gc` module

```python
import gc

# Garbage collection
gc.collect()  # Force collection
gc.disable()  # Disable collector
gc.enable()   # Enable collector
```

## Object Protocols

### Container Protocol
Objects that can contain other objects implement:
```python
class CustomContainer:
    def __len__(self):
        return len(self.items)

    def __getitem__(self, key):
        return self.items[key]

    def __setitem__(self, key, value):
        self.items[key] = value

    def __delitem__(self, key):
        del self.items[key]

    def __contains__(self, item):
        return item in self.items
```

### Iterator Protocol
Objects that support iteration implement:
```python
class CustomIterator:
    def __iter__(self):
        return self

    def __next__(self):
        if self.current >= len(self.items):
            raise StopIteration
        item = self.items[self.current]
        self.current += 1
        return item
```

### Attribute Protocol
Objects control attribute access through:
```python
class CustomObject:
    def __getattribute__(self, name):
        # Called for all attribute access
        return super().__getattribute__(name)

    def __getattr__(self, name):
        # Called when attribute not found
        return f"Attribute {name} not found"

    def __setattr__(self, name, value):
        # Called when setting attributes
        super().__setattr__(name, value)

    def __delattr__(self, name):
        # Called when deleting attributes
        super().__delattr__(name)
```

### Function Protocol
Objects can be callable by implementing:
```python
class CallableObject:
    def __call__(self, *args, **kwargs):
        print(f"Called with args: {args}, kwargs: {kwargs}")
```

### Context Manager Protocol
Objects can be used in `with` statements by implementing:
```python
class ContextManager:
    def __enter__(self):
        print("Entering context")
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        print("Exiting context")
        if exc_type:
            print(f"Exception: {exc_val}")
            return True  # Suppress exception
```

### String Representation Protocol
Objects can control their string representation:
```python
class CustomObject:
    def __str__(self):
        return "String representation"

    def __repr__(self):
        return "Detailed representation"

    def __format__(self, format_spec):
        return f"Formatted: {format_spec}"
```

## Special Methods

### Object Creation
```python
class CustomObject:
    def __new__(cls, *args, **kwargs):
        # Called before __init__
        return super().__new__(cls)

    def __init__(self, *args, **kwargs):
        # Called after __new__
        pass
```

### Comparison Methods
```python
class ComparableObject:
    def __eq__(self, other):
        return self.value == other.value

    def __lt__(self, other):
        return self.value < other.value

    def __le__(self, other):
        return self.value <= other.value

    def __gt__(self, other):
        return self.value > other.value

    def __ge__(self, other):
        return self.value >= other.value
```

### Numeric Operations
```python
class NumericObject:
    def __add__(self, other):
        return self.value + other.value

    def __sub__(self, other):
        return self.value - other.value

    def __mul__(self, other):
        return self.value * other.value

    def __truediv__(self, other):
        return self.value / other.value
```

## Best Practices

1. Use appropriate protocols
2. Implement special methods correctly
3. Handle memory management properly
4. Document object behavior
5. Use type hints
6. Follow Python's data model
7. Consider performance implications
8. Test object behavior

## Common Pitfalls

1. Circular references
2. Incorrect protocol implementation
3. Memory leaks
4. Inconsistent behavior
5. Poor error handling
6. Over-complicated objects
7. Ignoring immutability
8. Not using context managers

- [Data Model](data_model.md)
- [Classes](classes.md)
- Memory Management
- Protocols 