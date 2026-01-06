# Python Data Types

## Introduction

Python provides a rich set of built-in data types that make it easy to work with different kinds of data. This guide covers the fundamental data types and their common operations.

## Basic Types

### Numbers
- Integers (int)
- Floating-point numbers (float)
- Complex numbers (complex)
- Boolean values (bool)

### Strings
- String literals
- String operations
- String formatting
- String methods

### Collections
- Lists
- Tuples
- Dictionaries
- Sets

## Advanced Data Structures

### Collections Module
- deque (double-ended queue)
- Counter
- OrderedDict
- defaultdict
- ChainMap

### Working with Sequences

#### Unpacking
```python
# Basic unpacking
p = (4, 5)
x, y = p

# Using _ as throwaway variable
data = ['ACME', 50, 91.1, (2012, 12, 21)]
name, shares, price, date = data
_, shares, _, date = data

# Unpacking N elements
record = ('Dave', 'dave@example.com', '773-555-1212', '847-555-1212')
name, email, *phone_numbers = record

# String splitting and unpacking
line = 'nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false'
uname, *fields, homedir, sh = line.split(':')
```

#### Sorting and Grouping
```python
# Sorting dictionaries
rows = [
    {'fname': 'Brian', 'lname': 'Jones', 'uid': 1003},
    {'fname': 'David', 'lname': 'Beazley', 'uid': 1002},
    {'fname': 'John', 'lname': 'Cleese', 'uid': 1001},
    {'fname': 'Big', 'lname': 'Jones', 'uid': 1004}
]

from operator import itemgetter

# Sort by single key
rows_by_fname = sorted(rows, key=itemgetter('fname'))
rows_by_uid = sorted(rows, key=itemgetter('uid'))

# Sort by multiple keys
rows_by_lfname = sorted(rows, key=itemgetter('lname', 'fname'))

# Grouping records
from itertools import groupby
for date, items in groupby(rows, key=itemgetter('date')):
    print(date)
    for i in items:
        print('    ', i)
```

### Working with Collections

#### Deque
```python
from collections import deque

# Create a deque with maximum length
q = deque(maxlen=3)
q.append(1)
q.append(2)
q.append(3)
q.append(4)  # First element is removed
print(q)  # deque([2, 3, 4])

# Append to left
q.appendleft(4)
print(q)  # deque([4, 2, 3])
```

#### Finding Largest/Smallest Items
```python
import heapq

# Find N smallest items
smallest = heapq.nsmallest(3, items, key=lambda s: s['price'])

# Find N largest items
largest = heapq.nlargest(3, items, key=lambda s: s['price'])
```

## Data Manipulation

### Filtering and Subsetting
```python
# Filtering lists
from itertools import compress
addresses = ['a', 'b', 'c', 'd']
counts = [0, 3, 10, 4]
more5 = [n > 5 for n in counts]
list(compress(addresses, more5))

# Dictionary subsetting
prices = {'ACME': 45.23, 'AAPL': 612.78, 'IBM': 205.55}
p1 = {key: value for key, value in prices.items() if value > 200}
```

### Text Processing
```python
# String alignment
text = 'Hello World'
text.ljust(20)  # Left justify
text.rjust(20)  # Right justify
text.center(20)  # Center
```

## Date and Time

### Working with Time Objects
```python
from datetime import timedelta

# Create time deltas
a = timedelta(days=2, hours=6)
b = timedelta(hours=4.5)
c = a + b

# Access components
print(c.days)  # 2
print(c.seconds)  # 37800
print(c.total_seconds())  # 210600.0
```

## Best Practices

1. Choose the right data type for your needs
2. Use list comprehensions for simple transformations
3. Leverage built-in functions and methods
4. Consider memory usage for large datasets
5. Use appropriate data structures for performance

- Type Annotations
- [Data Structures](data_structures.md)
- [Collections Module](collections.md)
- [Date and Time](datetime.md) 

Next: [Lists & Tuples](lists_tuples.md)
