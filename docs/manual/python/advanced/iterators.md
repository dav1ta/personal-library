# Python Iterators and Generators

This guide covers Python iterators, generators, and related concepts for efficient iteration and data processing.

## Table of Contents
- [Introduction](#introduction)
- [Iterators](#iterators)
- [Generators](#generators)
- [itertools Module](#itertools-module)
- [Coroutines](#coroutines)
- [Async Iteration](#async-iteration)
- [Best Practices](#best-practices)

## Introduction

### What are Iterators?
Iterators are objects that implement the iterator protocol, allowing sequential access to elements in a collection. They provide a memory-efficient way to process data streams.

### Key Concepts
- Iterator protocol (`__iter__` and `__next__`)
- Generator functions and expressions
- Lazy evaluation
- Memory efficiency

## Iterators

### Basic Iterator Implementation
```python
class SequenceIterator:
    def __init__(self, start=0, step=1):
        self.current = start
        self.step = step

    def __iter__(self):
        return self

    def __next__(self):
        value = self.current
        self.current += self.step
        return value
```

### Iterator but Not Iterable
```python
class IteratorOnly:
    def __init__(self, data):
        self.data = data
        self.index = 0

    def __next__(self):
        if self.index >= len(self.data):
            raise StopIteration
        value = self.data[self.index]
        self.index += 1
        return value
```

### Sequence Iterables
```python
class MappedRange:
    """Apply a transformation to a range of numbers."""
    def __init__(self, transformation, start, end):
        self._transformation = transformation
        self._wrapped = range(start, end)

    def __getitem__(self, index):
        value = self._wrapped.__getitem__(index)
        result = self._transformation(value)
        return result

    def __len__(self):
        return len(self._wrapped)
```

## Generators

### Generator Functions
```python
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b
```

### Generator Expressions
```python
# List comprehension (eager evaluation)
squares_list = [x**2 for x in range(10)]

# Generator expression (lazy evaluation)
squares_gen = (x**2 for x in range(10))
```

### Nested Generators
```python
def _iterate_array2d(array2d):
    for i, row in enumerate(array2d):
        for j, cell in enumerate(row):
            yield (i, j), cell

def search_nested(array, desired_value):
    try:
        coord = next(
            coord
            for (coord, cell) in _iterate_array2d(array)
            if cell == desired_value
        )
    except StopIteration:
        raise ValueError(f"{desired_value} not found")
    return coord
```

## itertools Module

### Basic Functions
```python
from itertools import islice, filterfalse, takewhile, dropwhile

# Take first n elements
first_ten = islice(iterable, 10)

# Filter elements
filtered = filter(lambda x: x > 1000, iterable)

# Take while condition is true
taken = takewhile(lambda x: x < 100, iterable)

# Drop while condition is true
dropped = dropwhile(lambda x: x < 100, iterable)
```

### Advanced Functions
```python
from itertools import tee, chain, zip_longest

# Split iterator into multiple
iter1, iter2, iter3 = tee(original_iterator, 3)

# Chain multiple iterables
combined = chain(iterable1, iterable2, iterable3)

# Zip with padding
zipped = zip_longest(iterable1, iterable2, fillvalue=None)
```

## Coroutines

### Basic Coroutine
```python
def stream_data(db_handler):
    while True:
        try:
            yield db_handler.read_n_records(10)
        except CustomException as e:
            logger.info("controlled error %r, continuing", e)
        except Exception as e:
            logger.info("unhandled error %r, stopping", e)
            db_handler.close()
            break
```

### Coroutine Methods
```python
def stream_db_records(db_handler):
    retrieved_data = None
    previous_page_size = 10
    try:
        while True:
            page_size = yield retrieved_data
            if page_size is None:
                page_size = previous_page_size
            previous_page_size = page_size
            retrieved_data = db_handler.read_n_records(page_size)
    except GeneratorExit:
        db_handler.close()
```

### Coroutine Control
- `.close()`: Raises GeneratorExit at the current yield point
- `.throw()`: Raises an exception at the current yield point
- `.send()`: Sends a value to the coroutine

## Async Iteration

### Async Context Managers
```python
@contextlib.asynccontextmanager
async def db_management():
    try:
        await stop_database()
        yield
    finally:
        await start_database()
```

### Async Iterators
```python
class RecordStreamer:
    def __init__(self, max_rows=100):
        self._current_row = 0
        self._max_rows = max_rows

    def __aiter__(self):
        return self

    async def __anext__(self):
        if self._current_row < self._max_rows:
            row = (self._current_row, await coroutine())
            self._current_row += 1
            return row
        raise StopAsyncIteration
```

### Async Generators
```python
async def record_streamer(max_rows):
    current_row = 0
    while current_row < max_rows:
        row = (current_row, await coroutine())
        current_row += 1
        yield row
```

## Best Practices

1. **Memory Efficiency**
   - Use generators for large datasets
   - Avoid materializing entire sequences
   - Consider memory vs CPU trade-offs

2. **Error Handling**
   - Handle StopIteration appropriately
   - Clean up resources in finally blocks
   - Use context managers when possible

3. **Performance**
   - Use appropriate itertools functions
   - Consider using list comprehensions for small sequences
   - Profile memory usage for large datasets

4. **Code Organization**
   - Keep generators focused and single-purpose
   - Document iterator behavior
   - Use type hints for clarity

- [Data Model](../advanced/data_model.md)
- [Context Managers](../advanced/context_managers.md)
- [Async Programming](../advanced/async.md)
- Performance Optimization 

Next: [Async Programming](async.md)
