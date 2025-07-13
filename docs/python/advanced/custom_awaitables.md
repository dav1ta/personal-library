# Python Custom Awaitables

Custom awaitables allow you to create objects that can be used with `await` syntax, providing fine-grained control over asynchronous behavior beyond what standard coroutines and futures offer.

## Table of Contents

- [Understanding Awaitables](#understanding-awaitables)
- [The Awaitable Protocol](#the-awaitable-protocol)
- [Creating Custom Awaitables](#creating-custom-awaitables)
- [Practical Examples](#practical-examples)
- [Advanced Patterns](#advanced-patterns)
- [Best Practices](#best-practices)
- [Performance Considerations](#performance-considerations)

## Understanding Awaitables

An awaitable is any object that can be used with the `await` keyword. Python defines three main types of awaitables:

1. **Coroutines** - Functions defined with `async def`
2. **Tasks** - Wrappers around coroutines that can be scheduled
3. **Futures** - Low-level objects representing eventual results
4. **Custom awaitables** - Objects implementing the awaitable protocol

```python
import asyncio
from typing import Any, Generator

# Standard awaitable types
async def coroutine_example():
    return "I'm a coroutine"

async def main():
    # Coroutine
    result1 = await coroutine_example()
    
    # Task
    task = asyncio.create_task(coroutine_example())
    result2 = await task
    
    # Future
    future = asyncio.Future()
    future.set_result("I'm a future")
    result3 = await future
```

## The Awaitable Protocol

To create a custom awaitable, you need to implement the `__await__` method that returns an iterator:

```python
class CustomAwaitable:
    def __await__(self):
        # Must return an iterator that yields from the event loop
        # until the result is ready
        pass
```

The `__await__` method should return a generator that:
- Yields control back to the event loop when waiting
- Eventually returns the final result
- Can raise exceptions if needed

## Creating Custom Awaitables

### Basic Custom Awaitable

```python
import asyncio
from typing import Any, Generator

class TimedAwaitable:
    def __init__(self, delay: float, result: Any):
        self.delay = delay
        self.result = result
    
    def __await__(self) -> Generator[Any, None, Any]:
        # Yield from asyncio.sleep to actually wait
        yield from asyncio.sleep(self.delay).__await__()
        return self.result

# Usage
async def test_timed():
    result = await TimedAwaitable(2.0, "Hello after 2 seconds")
    print(result)

# Run: asyncio.run(test_timed())
```

### Awaitable with State Management

```python
class CountdownAwaitable:
    def __init__(self, start: int):
        self.current = start
        self.start = start
    
    def __await__(self) -> Generator[Any, None, int]:
        while self.current > 0:
            print(f"Countdown: {self.current}")
            yield from asyncio.sleep(1).__await__()
            self.current -= 1
        
        print("Countdown finished!")
        return self.start

# Usage
async def countdown_demo():
    original_count = await CountdownAwaitable(3)
    print(f"Started with: {original_count}")
```

### Awaitable with Error Handling

```python
class ReliableAwaitable:
    def __init__(self, operation, max_retries: int = 3):
        self.operation = operation
        self.max_retries = max_retries
    
    def __await__(self) -> Generator[Any, None, Any]:
        for attempt in range(self.max_retries):
            try:
                if asyncio.iscoroutinefunction(self.operation):
                    result = yield from self.operation().__await__()
                else:
                    result = self.operation()
                return result
            except Exception as e:
                if attempt == self.max_retries - 1:
                    raise e
                print(f"Attempt {attempt + 1} failed: {e}. Retrying...")
                yield from asyncio.sleep(0.5 * (attempt + 1)).__await__()

# Usage
async def unreliable_operation():
    import random
    if random.random() < 0.7:  # 70% chance of failure
        raise Exception("Random failure")
    return "Success!"

async def test_reliable():
    result = await ReliableAwaitable(unreliable_operation, max_retries=3)
    print(result)
```

## Practical Examples

### Rate-Limited Awaitable

```python
import time
from collections import deque

class RateLimitedAwaitable:
    def __init__(self, operation, rate_limit: float):
        self.operation = operation
        self.rate_limit = rate_limit  # seconds between calls
        self.last_call = 0
    
    def __await__(self) -> Generator[Any, None, Any]:
        current_time = time.time()
        time_since_last = current_time - self.last_call
        
        if time_since_last < self.rate_limit:
            wait_time = self.rate_limit - time_since_last
            yield from asyncio.sleep(wait_time).__await__()
        
        self.last_call = time.time()
        
        if asyncio.iscoroutinefunction(self.operation):
            result = yield from self.operation().__await__()
        else:
            result = self.operation()
        
        return result

# Usage
async def api_call():
    print(f"API called at {time.time()}")
    return "API response"

async def test_rate_limiting():
    # Ensure at least 1 second between calls
    rate_limited_call = RateLimitedAwaitable(api_call, 1.0)
    
    # These will be spaced 1 second apart
    await rate_limited_call
    await rate_limited_call
    await rate_limited_call
```

### Cache-Aware Awaitable

```python
import hashlib
import json
from typing import Dict, Any, Callable

class CachedAwaitable:
    _cache: Dict[str, Any] = {}
    
    def __init__(self, operation: Callable, *args, ttl: float = 300, **kwargs):
        self.operation = operation
        self.args = args
        self.kwargs = kwargs
        self.ttl = ttl  # Time to live in seconds
        self.cache_key = self._generate_cache_key()
    
    def _generate_cache_key(self) -> str:
        # Create a unique key based on operation and arguments
        key_data = {
            'operation': self.operation.__name__,
            'args': self.args,
            'kwargs': self.kwargs
        }
        key_str = json.dumps(key_data, sort_keys=True, default=str)
        return hashlib.md5(key_str.encode()).hexdigest()
    
    def __await__(self) -> Generator[Any, None, Any]:
        # Check cache first
        if self.cache_key in self._cache:
            cached_result, timestamp = self._cache[self.cache_key]
            if time.time() - timestamp < self.ttl:
                print("Cache hit!")
                return cached_result
        
        # Execute operation
        if asyncio.iscoroutinefunction(self.operation):
            result = yield from self.operation(*self.args, **self.kwargs).__await__()
        else:
            result = self.operation(*self.args, **self.kwargs)
        
        # Cache result
        self._cache[self.cache_key] = (result, time.time())
        print("Result cached")
        return result

# Usage
async def expensive_computation(n: int) -> int:
    print(f"Computing expensive operation for {n}")
    yield from asyncio.sleep(2).__await__()  # Simulate expensive work
    return n * n

async def test_caching():
    # First call - will compute
    result1 = await CachedAwaitable(expensive_computation, 5, ttl=10)
    print(f"Result 1: {result1}")
    
    # Second call - will use cache
    result2 = await CachedAwaitable(expensive_computation, 5, ttl=10)
    print(f"Result 2: {result2}")
```

### Progress-Tracking Awaitable

```python
from typing import Optional, Callable

class ProgressAwaitable:
    def __init__(self, operation: Callable, progress_callback: Optional[Callable] = None):
        self.operation = operation
        self.progress_callback = progress_callback
        self.progress = 0.0
    
    def update_progress(self, progress: float):
        self.progress = min(100.0, max(0.0, progress))
        if self.progress_callback:
            self.progress_callback(self.progress)
    
    def __await__(self) -> Generator[Any, None, Any]:
        self.update_progress(0.0)
        
        # Simulate work with progress updates
        steps = 10
        for i in range(steps):
            yield from asyncio.sleep(0.1).__await__()
            self.update_progress((i + 1) / steps * 100)
        
        # Execute the actual operation
        if asyncio.iscoroutinefunction(self.operation):
            result = yield from self.operation().__await__()
        else:
            result = self.operation()
        
        return result

# Usage
def progress_printer(progress: float):
    bar_length = 20
    filled_length = int(bar_length * progress / 100)
    bar = '█' * filled_length + '-' * (bar_length - filled_length)
    print(f'\rProgress: |{bar}| {progress:.1f}%', end='', flush=True)

async def some_work():
    return "Work completed!"

async def test_progress():
    result = await ProgressAwaitable(some_work, progress_printer)
    print(f"\n{result}")
```

## Advanced Patterns

### Composable Awaitables

```python
class ComposableAwaitable:
    def __init__(self, operation: Callable):
        self.operation = operation
        self.middleware = []
    
    def add_middleware(self, middleware: Callable):
        """Add middleware that wraps the operation"""
        self.middleware.append(middleware)
        return self
    
    def __await__(self) -> Generator[Any, None, Any]:
        # Apply middleware in reverse order (last added, first executed)
        wrapped_operation = self.operation
        for middleware in reversed(self.middleware):
            wrapped_operation = middleware(wrapped_operation)
        
        if asyncio.iscoroutinefunction(wrapped_operation):
            result = yield from wrapped_operation().__await__()
        else:
            result = wrapped_operation()
        
        return result

# Middleware examples
def timing_middleware(operation):
    async def wrapper(*args, **kwargs):
        start_time = time.time()
        if asyncio.iscoroutinefunction(operation):
            result = await operation(*args, **kwargs)
        else:
            result = operation(*args, **kwargs)
        end_time = time.time()
        print(f"Operation took {end_time - start_time:.2f} seconds")
        return result
    return wrapper

def logging_middleware(operation):
    async def wrapper(*args, **kwargs):
        print(f"Executing operation: {operation.__name__}")
        if asyncio.iscoroutinefunction(operation):
            result = await operation(*args, **kwargs)
        else:
            result = operation(*args, **kwargs)
        print(f"Operation completed: {operation.__name__}")
        return result
    return wrapper

# Usage
async def my_operation():
    await asyncio.sleep(1)
    return "Done"

async def test_composable():
    awaitable = (ComposableAwaitable(my_operation)
                .add_middleware(timing_middleware)
                .add_middleware(logging_middleware))
    
    result = await awaitable
    print(result)
```

### Context-Aware Awaitables

```python
import contextvars
from typing import Dict, Any

# Context variable for tracking operation context
operation_context: contextvars.ContextVar[Dict[str, Any]] = contextvars.ContextVar(
    'operation_context', default={}
)

class ContextAwaitable:
    def __init__(self, operation: Callable, context: Dict[str, Any] = None):
        self.operation = operation
        self.context = context or {}
    
    def __await__(self) -> Generator[Any, None, Any]:
        # Get current context and merge with our context
        current_context = operation_context.get({})
        merged_context = {**current_context, **self.context}
        
        # Set context for this operation
        token = operation_context.set(merged_context)
        
        try:
            if asyncio.iscoroutinefunction(self.operation):
                result = yield from self.operation().__await__()
            else:
                result = self.operation()
            return result
        finally:
            # Reset context
            operation_context.reset(token)

# Usage
async def context_aware_operation():
    context = operation_context.get({})
    user_id = context.get('user_id', 'unknown')
    request_id = context.get('request_id', 'unknown')
    print(f"Processing for user {user_id}, request {request_id}")
    return f"Result for {user_id}"

async def test_context():
    # Set initial context
    result = await ContextAwaitable(
        context_aware_operation, 
        {'user_id': '12345', 'request_id': 'req-001'}
    )
    print(result)
```

## Best Practices

### 1. Always Yield Control

```python
# ❌ Bad - blocks the event loop
class BlockingAwaitable:
    def __await__(self):
        time.sleep(1)  # Blocks!
        return "result"

# ✅ Good - yields control to event loop
class NonBlockingAwaitable:
    def __await__(self):
        yield from asyncio.sleep(1).__await__()
        return "result"
```

### 2. Handle Cancellation

```python
class CancellableAwaitable:
    def __init__(self, operation):
        self.operation = operation
        self.cancelled = False
    
    def __await__(self):
        try:
            if asyncio.iscoroutinefunction(self.operation):
                result = yield from self.operation().__await__()
            else:
                result = self.operation()
            return result
        except asyncio.CancelledError:
            print("Operation was cancelled")
            # Cleanup code here
            raise
```

### 3. Provide Clear Error Messages

```python
class ValidatingAwaitable:
    def __init__(self, operation, validator=None):
        self.operation = operation
        self.validator = validator
    
    def __await__(self):
        try:
            if asyncio.iscoroutinefunction(self.operation):
                result = yield from self.operation().__await__()
            else:
                result = self.operation()
            
            if self.validator and not self.validator(result):
                raise ValueError(f"Operation result failed validation: {result}")
            
            return result
        except Exception as e:
            # Wrap with more context
            raise RuntimeError(f"ValidatingAwaitable failed: {e}") from e
```

### 4. Make Awaitables Reusable When Appropriate

```python
class ReusableAwaitable:
    def __init__(self, operation):
        self.operation = operation
        self._result = None
        self._computed = False
    
    def __await__(self):
        if self._computed:
            # Return cached result immediately
            if False:  # This makes it a generator
                yield
            return self._result
        
        # Compute result
        if asyncio.iscoroutinefunction(self.operation):
            self._result = yield from self.operation().__await__()
        else:
            self._result = self.operation()
        
        self._computed = True
        return self._result
```

## Performance Considerations

1. **Memory Usage**: Custom awaitables that hold state should be careful about memory leaks
2. **Generator Overhead**: Each `__await__` method creates a generator - consider this for high-frequency operations
3. **Exception Handling**: Proper exception handling prevents resource leaks
4. **Cancellation**: Always handle `CancelledError` to clean up resources

## Related Topics

- [Async Programming](async.md) - General async programming concepts
- [Context Managers](context_managers.md) - For async context managers
- [Testing](testing.md) - Testing async code with custom awaitables

## Examples Repository

For more examples and use cases, see the [FastAPI Examples](../fastapi/examples/) which demonstrate custom awaitables in web applications. 