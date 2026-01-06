# Python Objects and References

## Introduction

Understanding how Python handles objects and references is crucial for writing efficient and correct code. This guide covers object identity, references, and memory management in Python.

## Object Identity and References

### Variables as References
```python
# Variables are labels attached to objects
a = [1, 2, 3]
b = a  # Both a and b refer to the same list
b.append(4)
print(a)  # [1, 2, 3, 4]
```

### Object Identity
```python
# id() returns the object's memory address
a = [1, 2, 3]
b = [1, 2, 3]
print(id(a) == id(b))  # False
print(a is b)  # False
```

## Object Copies

### Shallow Copies
```python
# List copying
original = [1, [2, 3]]
shallow = original.copy()  # or list(original)
shallow[1][0] = 4
print(original)  # [1, [4, 3]]

# Dict copying
d = {'a': [1, 2]}
d_copy = d.copy()
```

### Deep Copies
```python
import copy

original = [1, [2, 3]]
deep = copy.deepcopy(original)
deep[1][0] = 4
print(original)  # [1, [2, 3]]
```

## Function Parameters

### Reference Behavior
```python
def modify_list(lst):
    lst.append(4)  # Modifies the original list

def reassign_list(lst):
    lst = [1, 2, 3]  # Creates a new local reference

numbers = [1, 2, 3]
modify_list(numbers)
print(numbers)  # [1, 2, 3, 4]

reassign_list(numbers)
print(numbers)  # [1, 2, 3, 4]
```

## Memory Management

### Reference Counting
```python
import sys

# Check reference count
a = []
print(sys.getrefcount(a))  # 2 (one for a, one for getrefcount parameter)
```

### Garbage Collection
```python
import gc

# Force garbage collection
gc.collect()

# Disable/enable garbage collection
gc.disable()
gc.enable()
```

## Weak References

### Basic Usage
```python
import weakref

# Create weak reference
a_set = {1, 2, 3}
wref = weakref.ref(a_set)
print(wref())  # {1, 2, 3}

# Check if referent is alive
del a_set
print(wref())  # None
```

### WeakValueDictionary
```python
import weakref

class Cheese:
    def __init__(self, kind):
        self.kind = kind

# Create cache
stock = weakref.WeakValueDictionary()
catalog = [Cheese('Red Leicester'), Cheese('Tilsit'),
           Cheese('Brie'), Cheese('Parmesan')]

# Add to cache
for cheese in catalog:
    stock[cheese.kind] = cheese

# Remove catalog
del catalog
print(sorted(stock.keys()))  # ['Parmesan']
```

## Special Cases

### Tuple Slicing
```python
# Tuple slicing returns the same object
t = (1, 2, 3)
print(t[:] is t)  # True
```

### Custom Types
```python
# Make list subclass weak-referenceable
class MyList(list):
    """list subclass whose instances may be weakly referenced"""
    pass

a_list = MyList(range(10))
wref = weakref.ref(a_list)
```

## Best Practices

1. Understand reference semantics
2. Use appropriate copy methods
3. Be careful with mutable default arguments
4. Use weak references for caching
5. Consider memory management
6. Use `is` for identity comparison
7. Use `==` for value comparison

- [Data Types](data_types.md)
- Memory Management
- Garbage Collection
- [Performance Optimization](performance.md) 

Next: [Functions](functions.md)
