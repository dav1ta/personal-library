# Iterators

## Generators

Generators were introduced in Python a long time ago (PEP-255), with the idea of introducing iteration in Python while improving the performance of the program (by using less memory) at the same time. The idea of a generator is to create an object that is iterable and, while it's being iterated, will produce the elements it contains, one at a time. The main use of generators is to save memory—instead of having a very large list of elements in memory, holding everything at once, we have an object that knows how to produce each particular element, one at a time, as it is required. This feature enables lazy computations of heavyweight objects in memory, in a similar manner to what other functional programming languages (Haskell, for instance) provide. It would even be possible to work with infinite sequences because the lazy nature of generators enables such an option.

## `next()`

The `next()` built-in function will advance the iterable to its next element and return it.

## `itertools`

```python
def process(self):
    for purchase in self.purchases:
        if purchase > 1000.0:
            ...
```

This is not only non-Pythonic, but it's also rigid (and rigidity is a trait that denotes bad code). It doesn't handle changes very well. What if the number changes now? Do we pass it by parameter? What if we need more than one? What if the condition is different (less than, for instance)? Do we pass a lambda? These questions should not be answered by this object, whose sole responsibility is to compute a set of well-defined metrics over a stream of purchases represented as numbers. And, of course, the answer is no. It would be a huge mistake to make such a change (once again, clean code is flexible, and we don't want to make it rigid by coupling this object to external factors). These requirements will have to be addressed elsewhere.

### `itertools.islice` - Takes First Ten

```python
from itertools import islice

purchases = islice(filter(lambda p: p > 1000.0, purchases), 10)
```

There is no memory penalization for filtering this way because since they are all generators, the evaluation is always lazy. This gives us the power of thinking as if we had filtered the entire set at once and then passed it to the object, but without actually fitting everything in memory. Keep in mind the trade-off mentioned at the beginning of the chapter, between memory and CPU usage. While the code might use less memory, it could take up more CPU time, but most of the times, this is acceptable when we have to process lots of objects in memory while keeping the code maintainable.

## Repeated Iterations with `itertools.tee`

```python
def process_purchases(purchases):
    min_, max_, avg = itertools.tee(purchases, 3)
    return min(min_), max(max_), median(avg)
```

In this example, `itertools.tee` will split the original iterable into three new ones. We will use each of these for the different kinds of iterations that we require, without needing to repeat three different loops over purchases.

## Yielding

```python
def _iterate_array2d(array2d):
    for i, row in enumerate(array2d):
        for j, cell in enumerate(row):
            yield (i, j), cell
```

```python
def search_nested(array, desired_value):
    try:
        coord = next(
            coord
            for (coord, cell) in _iterate_array2d(array)
            if cell == desired_value
        )
    except StopIteration as e:
        raise ValueError(f"{desired_value} not found") from e
    logger.info("value %r found at [%i, %i]", desired_value, *coord)
    return coord
```

## Iterator but Not Iterable

```python
class SequenceIterator:
    def __init__(self, start=0, step=1):
        self.current = start
        self.step = step
    def __next__(self):
        value = self.current
        self.current += self.step
        return value
```

### Sequence are Iterables

```python
class MappedRange:
    """Apply a transformation to a range of numbers."""
    def __init__(self, transformation, start, end):
        self._transformation = transformation
        self._wrapped = range(start, end)
    def __getitem__(self, index):
        value = self._wrapped.__getitem__(index)
        result = self._transformation(value)
        logger.info("Index %d: %s", index, result)
        return result
    def __len__(self):
        return len(self._wrapped)
```

## Coroutines

- `.close()`
- `.throw()`
- `.send()`

Python takes advantage of generators in order to create coroutines. Because generators can naturally suspend, they're a convenient starting point. But generators weren't enough as they were originally thought to be, so these methods were added. This is because typically, it's not enough to just be able to suspend some part of the code; you'd also want to communicate with it (pass data and signal changes in the context).

### `close()`

When calling this method, the generator will receive the `GeneratorExit` exception. If it's not handled, then the generator will finish without producing any more values, and its iteration will stop.

### `throw()`

This method will throw the exception at the line where the generator is currently suspended. If the generator handles the exception that was sent, the code in that particular `except` clause will be called; otherwise, the exception will propagate to the caller.

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

### `send(value)`

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

First None

## `yield from`

`yield from iterable`

## Async Programming

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

```python
import asyncio
import random

async def coroutine():
    await asyncio.sleep(0.1)
    return random.randint(1, 10000)

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

`await async_iterator.__anext__()`

### Async Generators

```python
async def record_streamer(max_rows):
    current_row = 0
    while current_row < max_rows:
        row = (current_row, await coroutine())
        current_row += 1
        yield row
```

Next: [Iterators (Advanced)](../advanced/iterators.md)
