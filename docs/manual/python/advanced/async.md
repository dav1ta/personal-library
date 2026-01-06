# Asynchronous Programming in Python

This guide covers asynchronous programming in Python, from basic concepts to advanced patterns and best practices.

## Table of Contents
- [Introduction](#introduction)
- [Core Concepts](#core-concepts)
- [Basic Usage](#basic-usage)
- [Advanced Patterns](#advanced-patterns)
- [Integration with Libraries](#integration-with-libraries)
- [Best Practices](#best-practices)
- [Common Patterns](#common-patterns)

## Introduction

### What is Asynchronous Programming?
Asynchronous programming is a method that allows for the execution of certain tasks concurrently without blocking the main thread. Instead of waiting for one task to complete before moving on to the next, asynchronous programming allows multiple tasks to run in "parallel", making better use of system resources and often speeding up overall execution.

### When to Use Async
- I/O-bound operations (network requests, file operations)
- Web applications and APIs
- Database operations
- Long-running tasks that involve waiting

### Async vs Threading
- **Async**: Single-threaded, event-loop based
- **Threading**: Multiple threads running in parallel
- **Use Async When**: I/O-bound operations, many concurrent tasks
- **Use Threading When**: CPU-bound operations, true parallelism needed

## Core Concepts

### Event Loop
The event loop is the heart of every asyncio application. It manages the execution of coroutines and callbacks.

```python
import asyncio

async def main():
    print("Hello")
    await asyncio.sleep(1)
    print("World")

# Run the event loop
asyncio.run(main())
```

### Coroutines
Coroutines are special functions that can pause their execution and yield control back to the event loop.

```python
async def my_coroutine():
    print("Starting")
    await asyncio.sleep(1)
    print("Finished")
```

### Tasks
Tasks are used to schedule coroutines concurrently.

```python
async def main():
    # Create tasks
    task1 = asyncio.create_task(my_coroutine())
    task2 = asyncio.create_task(my_coroutine())
    
    # Wait for tasks to complete
    await task1
    await task2
```

## Basic Usage

### Creating Async Functions
```python
async def fetch_data():
    # Simulate I/O operation
    await asyncio.sleep(1)
    return "Data"

async def process_data():
    data = await fetch_data()
    return data.upper()
```

### Running Multiple Tasks
```python
async def main():
    # Create multiple tasks
    tasks = [
        asyncio.create_task(fetch_data()),
        asyncio.create_task(fetch_data()),
        asyncio.create_task(fetch_data())
    ]
    
    # Wait for all tasks to complete
    results = await asyncio.gather(*tasks)
    return results
```

### Error Handling
```python
async def safe_operation():
    try:
        result = await risky_operation()
        return result
    except Exception as e:
        print(f"Error occurred: {e}")
        return None
```

## Advanced Patterns

### Task Management
```python
async def main():
    # Create tasks
    tasks = [asyncio.create_task(fetch_data()) for _ in range(3)]
    
    # Wait for first completed task
    done, pending = await asyncio.wait(
        tasks,
        return_when=asyncio.FIRST_COMPLETED
    )
    
    # Cancel remaining tasks
    for task in pending:
        task.cancel()
```

### Timeouts
```python
async def main():
    try:
        # Set timeout for operation
        result = await asyncio.wait_for(
            long_operation(),
            timeout=5.0
        )
        return result
    except asyncio.TimeoutError:
        print("Operation timed out")
        return None
```

### Semaphores
```python
async def main():
    # Limit concurrent operations
    sem = asyncio.Semaphore(3)
    
    async def bounded_operation():
        async with sem:
            return await operation()
    
    tasks = [bounded_operation() for _ in range(10)]
    return await asyncio.gather(*tasks)
```

## Integration with Libraries

### HTTP Requests (aiohttp)
```python
import aiohttp

async def fetch_url(session, url):
    async with session.get(url) as response:
        return await response.text()

async def main():
    async with aiohttp.ClientSession() as session:
        urls = ["http://example.com", "http://example.org"]
        tasks = [fetch_url(session, url) for url in urls]
        results = await asyncio.gather(*tasks)
        return results
```

### Database Operations (aiomysql)
```python
import aiomysql

async def fetch_data():
    pool = await aiomysql.create_pool(
        host='localhost',
        user='user',
        password='password',
        db='database'
    )
    
    async with pool.acquire() as conn:
        async with conn.cursor() as cur:
            await cur.execute("SELECT * FROM table")
            return await cur.fetchall()
```

### File Operations (aiofiles)
```python
import aiofiles

async def read_file(filename):
    async with aiofiles.open(filename, mode='r') as f:
        return await f.read()

async def write_file(filename, content):
    async with aiofiles.open(filename, mode='w') as f:
        await f.write(content)
```

## Best Practices

1. **Error Handling**
   - Always handle exceptions in async functions
   - Use proper error propagation
   - Implement cleanup in finally blocks

2. **Resource Management**
   - Use async context managers
   - Properly close connections and files
   - Implement proper cleanup

3. **Performance**
   - Use appropriate concurrency limits
   - Monitor memory usage
   - Profile async operations

4. **Code Organization**
   - Keep async functions focused
   - Use proper naming conventions
   - Document async behavior

## Common Patterns

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
class AsyncIterator:
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

- [Threading](../advanced/threading.md)
- Concurrency Patterns
- Performance Optimization
- Debugging 

Next: [Asyncio (Guide)](../async.md)
