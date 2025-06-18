# Collections in Python

Python provides several built-in collection types for storing and organizing data.

## List
A mutable, ordered sequence of items.
```python
fruits = ["apple", "banana", "cherry"]
```

## Tuple
An immutable, ordered sequence of items.
```python
point = (10, 20)
```

## Set
An unordered collection of unique items.
```python
unique_numbers = {1, 2, 3, 2}
# {1, 2, 3}
```

## Dictionary
A collection of key-value pairs.
```python
person = {"name": "Alice", "age": 30}
```

## The collections Module
The `collections` module provides additional data structures:
- `deque`: Fast appends and pops from both ends
- `Counter`: Counts hashable objects
- `defaultdict`: Dictionary with default values
- `OrderedDict`: Dictionary that remembers insertion order
- `namedtuple`: Factory for tuple subclasses with named fields

## Example
```python
from collections import Counter
words = ["apple", "banana", "apple"]
count = Counter(words)
print(count)  # Counter({'apple': 2, 'banana': 1})
```

## Resources
- [Python collections docs](https://docs.python.org/3/library/collections.html) 