# Python Built-in Types and Operations

## Introduction

Python provides a rich set of built-in types and operations. This guide covers the most commonly used built-in types and their operations.

## Bytes and Bytearrays

### Basic Operations
```python
# Bytes creation
b = b'Hello'
b = bytes([72, 101, 108, 108, 111])

# Bytearray creation
ba = bytearray(b'Hello')
ba = bytearray([72, 101, 108, 108, 111])
```

### Common Operations
```python
# Concatenation
b1 = b'Hello'
b2 = b'World'
b3 = b1 + b2

# Repetition
b4 = b1 * 3

# Slicing
b5 = b1[1:4]

# Methods
b1.capitalize()
b1.center(10)
b1.count(b'l')
b1.decode('utf-8')
b1.endswith(b'o')
b1.find(b'l')
b1.isalnum()
b1.isalpha()
b1.isdigit()
b1.islower()
b1.isupper()
b1.join([b2, b3])
b1.lower()
b1.replace(b'l', b'L')
b1.split()
b1.strip()
b1.upper()
```

### Bytearray-Specific Operations
```python
# Modification
ba[0] = 72
ba[1:3] = b'el'
ba.append(33)
ba.extend(b'!')
ba.insert(0, 72)
ba.pop()
ba.remove(72)
ba.reverse()
```

## Dictionaries

### Basic Operations
```python
# Dictionary creation
d = {'a': 1, 'b': 2}
d = dict(a=1, b=2)

# Access and modification
value = d['a']
d['c'] = 3
del d['b']
```

### Common Methods
```python
# Dictionary methods
d.clear()
d.copy()
d.fromkeys(['a', 'b', 'c'], 0)
d.get('a', default=0)
d.items()
d.keys()
d.pop('a')
d.popitem()
d.setdefault('d', 4)
d.update({'e': 5})
d.values()
```

### Dictionary Operations
```python
# Merging dictionaries
d1 = {'a': 1}
d2 = {'b': 2}
d3 = d1 | d2  # Python 3.9+

# Membership testing
'a' in d
len(d)
```

## Sets

### Basic Operations
```python
# Set creation
s = {1, 2, 3}
s = set([1, 2, 3])

# Modification
s.add(4)
s.remove(1)
s.discard(1)
s.pop()
s.clear()
```

### Set Operations
```python
# Set operations
s1 = {1, 2, 3}
s2 = {3, 4, 5}

# Union
s3 = s1 | s2
s3 = s1.union(s2)

# Intersection
s4 = s1 & s2
s4 = s1.intersection(s2)

# Difference
s5 = s1 - s2
s5 = s1.difference(s2)

# Symmetric difference
s6 = s1 ^ s2
s6 = s1.symmetric_difference(s2)
```

### Set Methods
```python
# Set methods
s1.update(s2)
s1.intersection_update(s2)
s1.difference_update(s2)
s1.symmetric_difference_update(s2)
s1.issubset(s2)
s1.issuperset(s2)
s1.isdisjoint(s2)
```

## Strings

### Basic Operations
```python
# String creation
s = 'Hello'
s = "World"
s = '''Multi-line
string'''

# Concatenation and repetition
s1 = 'Hello'
s2 = 'World'
s3 = s1 + ' ' + s2
s4 = s1 * 3
```

### String Methods
```python
# Case manipulation
s.capitalize()
s.casefold()
s.lower()
s.upper()
s.title()
s.swapcase()

# Searching and replacing
s.count('l')
s.find('l')
s.index('l')
s.replace('l', 'L')
s.startswith('He')
s.endswith('o')

# Splitting and joining
s.split()
s.splitlines()
s.join(['a', 'b', 'c'])

# Stripping
s.strip()
s.lstrip()
s.rstrip()

# Alignment
s.center(10)
s.ljust(10)
s.rjust(10)
s.zfill(10)
```

### String Formatting
```python
# Format method
'{} {}'.format('Hello', 'World')
'{0} {1}'.format('Hello', 'World')
'{name} {greeting}'.format(name='Hello', greeting='World')

# f-strings
name = 'World'
f'Hello {name}'
```

## Tuples

### Basic Operations
```python
# Tuple creation
t = (1, 2, 3)
t = tuple([1, 2, 3])

# Access
value = t[0]
slice = t[1:3]

# Concatenation and repetition
t1 = (1, 2)
t2 = (3, 4)
t3 = t1 + t2
t4 = t1 * 3
```

### Tuple Methods
```python
# Tuple methods
t.count(1)
t.index(2)
```

## Best Practices

1. Choose appropriate data types
2. Use built-in methods efficiently
3. Consider memory usage
4. Use type hints
5. Follow Python's style guide
6. Document complex operations
7. Handle edge cases
8. Consider performance implications

- [Data Types](data_types.md)
- Type Annotations
- [Collections Module](collections.md)
- [Performance Optimization](performance.md) 