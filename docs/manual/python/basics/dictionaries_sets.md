# Python Dictionaries and Sets

## Introduction

Dictionaries and sets are two of Python's most powerful built-in data structures. This guide covers their implementation, usage, and best practices.

## Dictionaries

### Basic Operations
```python
# Creating dictionaries
d = {'key': 'value'}
d = dict(key='value')

# Accessing values
value = d['key']
value = d.get('key', default_value)

# Setting values
d['key'] = 'new_value'
d.setdefault('key', []).append(new_value)
```

### Dictionary Variations

#### OrderedDict
```python
from collections import OrderedDict
ordered = OrderedDict()
ordered['a'] = 1
ordered['b'] = 2
# Maintains insertion order
```

#### ChainMap
```python
from collections import ChainMap
# Search through multiple mappings
chain = ChainMap(locals(), globals(), vars(builtins))
```

#### Counter
```python
from collections import Counter
# Count occurrences of elements
counts = Counter(['a', 'b', 'a', 'c'])
print(counts['a'])  # 2
```

### Custom Dictionaries

#### UserDict
```python
from collections import UserDict
class MyDict(UserDict):
    def __missing__(self, key):
        return []  # Default value for missing keys
```

#### TypedDict
```python
from typing import TypedDict
class Point(TypedDict):
    x: int
    y: int
```

## Sets

### Basic Operations
```python
# Creating sets
s = {1, 2, 3}
s = set([1, 2, 3])

# Adding and removing elements
s.add(4)
s.remove(1)
s.discard(1)  # Safe removal
```

### Set Theory Operations
```python
a = {1, 2, 3}
b = {3, 4, 5}

# Union
print(a | b)  # {1, 2, 3, 4, 5}

# Intersection
print(a & b)  # {3}

# Difference
print(a - b)  # {1, 2}

# Symmetric difference
print(a ^ b)  # {1, 2, 4, 5}
```

### Frozen Sets
```python
# Immutable sets
fs = frozenset([1, 2, 3])
# Can be used as dictionary keys or set elements
```

## Hashability

### What Makes an Object Hashable?
- Must have a `__hash__()` method
- Must have an `__eq__()` method
- Hash value must never change during object's lifetime
- Objects that compare equal must have the same hash value

### User-Defined Types
```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def __hash__(self):
        return hash((self.x, self.y))
    
    def __eq__(self, other):
        if not isinstance(other, Point):
            return NotImplemented
        return (self.x, self.y) == (other.x, other.y)
```

## Implementation Details

### Dictionary Implementation
- Hash table based
- O(1) average case for lookups
- Maintains insertion order (Python 3.7+)
- Resizes when 2/3 full

### Set Implementation
- Hash table based
- O(1) average case for operations
- Maintains unique elements
- Resizes when 2/3 full

## Best Practices

1. Use dictionaries for key-value mappings
2. Use sets for unique collections
3. Choose appropriate dictionary variations
4. Consider hashability when using custom types
5. Use `get()` or `setdefault()` for safe access
6. Leverage set operations for efficient comparisons

- [Data Types](data_types.md)
- [Collections Module](collections.md)
- Type Annotations
- [Performance Optimization](performance.md) 

Next: [Objects](objects.md)
