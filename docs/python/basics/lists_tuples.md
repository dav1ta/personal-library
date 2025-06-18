# Python Lists and Tuples

## Introduction

Lists and tuples are two of Python's most commonly used sequence types. While lists are mutable, tuples are immutable. This guide covers their usage, operations, and best practices.

## Lists

### Basic Operations
```python
# Creating lists
lst = [1, 2, 3]
lst = list(range(3))

# Accessing elements
first = lst[0]
last = lst[-1]
slice = lst[1:3]

# Modifying lists
lst.append(4)
lst.extend([5, 6])
lst.insert(0, 0)
lst.remove(3)
popped = lst.pop()
```

### List Comprehensions
```python
# Basic comprehension
squares = [x**2 for x in range(10)]

# With condition
even_squares = [x**2 for x in range(10) if x % 2 == 0]

# Nested comprehension
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flattened = [x for row in matrix for x in row]

# Multiple conditions
result = [x for x in range(100) if x % 2 == 0 if x % 3 == 0]
```

### List Methods
```python
# Sorting
lst.sort()  # In-place
sorted_lst = sorted(lst)  # New list

# Reversing
lst.reverse()  # In-place
reversed_lst = list(reversed(lst))  # New list

# Searching
index = lst.index(3)  # Find first occurrence
count = lst.count(3)  # Count occurrences
```

## Tuples

### Basic Operations
```python
# Creating tuples
t = (1, 2, 3)
t = tuple([1, 2, 3])
t = 1, 2, 3  # Parentheses optional

# Accessing elements
first = t[0]
last = t[-1]
slice = t[1:3]

# Tuple unpacking
x, y, z = t
x, *rest = t  # Extended unpacking
```

### Named Tuples
```python
from collections import namedtuple
Point = namedtuple('Point', ['x', 'y'])
p = Point(1, 2)
print(p.x, p.y)  # 1 2
```

## Common Operations

### Slicing
```python
# Basic slicing
lst[1:4]  # Elements 1 to 3
lst[::2]  # Every second element
lst[::-1]  # Reverse

# Step
lst[1:4:2]  # Elements 1 and 3
```

### Concatenation and Repetition
```python
# Lists
combined = [1, 2] + [3, 4]
repeated = [1, 2] * 3

# Tuples
combined = (1, 2) + (3, 4)
repeated = (1, 2) * 3
```

### Membership Testing
```python
# Lists and tuples
3 in [1, 2, 3]  # True
3 not in (1, 2)  # True
```

## Performance Considerations

### Lists
- O(1) for indexing and appending
- O(n) for inserting and deleting
- Dynamic resizing
- Memory overhead for flexibility

### Tuples
- O(1) for indexing
- Immutable
- Fixed size
- Memory efficient
- Can be used as dictionary keys

## Best Practices

1. Use lists for mutable sequences
2. Use tuples for immutable sequences
3. Use list comprehensions for simple transformations
4. Use named tuples for structured data
5. Consider performance implications
6. Use appropriate methods for operations

- [Data Types](data_types.md)
- Iterators and Generators
- [Collections Module](collections.md)
- [Performance Optimization](performance.md) 