# Data Structures in Python

Data structures are ways to organize and store data efficiently. Python provides several built-in data structures:

## List
A dynamic array for ordered data.
```python
numbers = [1, 2, 3]
```

## Tuple
Immutable, ordered data.
```python
point = (1, 2)
```

## Set
Unordered, unique items.
```python
unique = {1, 2, 3}
```

## Dictionary
Key-value pairs.
```python
person = {"name": "Alice", "age": 30}
```

## Stack
Use a list with `append()` and `pop()`.

## Queue
Use `collections.deque` for efficient FIFO.

## Example: Stack
```python
stack = []
stack.append(1)
stack.append(2)
print(stack.pop())  # 2
```

## Resources
- [Python data structures](https://docs.python.org/3/tutorial/datastructures.html) 

Next: [Datetime](datetime.md)
