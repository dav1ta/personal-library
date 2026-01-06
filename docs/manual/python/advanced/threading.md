# Python Threading and Synchronization

This guide covers Python's threading module and synchronization primitives, from basic concepts to advanced patterns.

## Table of Contents
- [Introduction](#introduction)
- [Basic Threading](#basic-threading)
- [Synchronization Primitives](#synchronization-primitives)
- [Thread Safety](#thread-safety)
- [Common Patterns](#common-patterns)
- [Best Practices](#best-practices)

## Introduction

### What is Threading?
Threading in Python allows multiple threads of execution to run concurrently within a single process. While Python's Global Interpreter Lock (GIL) limits true parallelism, threading is still useful for I/O-bound tasks and concurrent operations.

### When to Use Threading
- I/O-bound operations
- Concurrent network requests
- Background tasks
- User interface responsiveness

## Basic Threading

### Creating Threads
```python
import threading

def worker():
    print("Worker thread running")

# Create and start a thread
thread = threading.Thread(target=worker)
thread.start()
thread.join()  # Wait for thread to complete
```

### Thread with Arguments
```python
def worker(name, count):
    for i in range(count):
        print(f"{name}: {i}")

thread = threading.Thread(
    target=worker,
    args=("Thread-1", 5),
    name="WorkerThread"
)
```

### Thread Local Storage
```python
thread_local = threading.local()

def worker():
    thread_local.value = threading.current_thread().name
    print(f"Thread local value: {thread_local.value}")
```

## Synchronization Primitives

### Lock
The simplest synchronization primitive, allowing one thread at a time to access a resource.

```python
import threading

lock = threading.Lock()

def critical_section():
    with lock:
        # Only one thread can access this section at a time
        print("Accessing critical section")
```

### RLock (Reentrant Lock)
Allows a thread to acquire the same lock multiple times.

```python
rlock = threading.RLock()

def recursive_function():
    with rlock:
        print("Lock acquired")
        # Can acquire the lock again
        if some_condition:
            recursive_function()
```

### Semaphore
Limits the number of threads that can access a resource.

```python
semaphore = threading.Semaphore(3)  # Allows up to 3 threads

def limited_access():
    with semaphore:
        print("Accessing limited resource")
```

### Event
Signals between threads.

```python
event = threading.Event()

def wait_for_event():
    print("Waiting for event...")
    event.wait()
    print("Event has been set")

def set_event():
    print("Setting event")
    event.set()
```

### Condition
Allows threads to wait for a condition to be met.

```python
condition = threading.Condition()

def consumer():
    with condition:
        print("Consumer waiting")
        condition.wait()  # Wait for a signal
        print("Consumer notified")

def producer():
    with condition:
        print("Producer notifying")
        condition.notify()  # Notify one waiting thread
```

### Barrier
Synchronizes a fixed number of threads at a specific point.

```python
barrier = threading.Barrier(3)

def task():
    print("Thread waiting at barrier")
    barrier.wait()  # Wait until all threads arrive
    print("Thread proceeding")
```

## Thread Safety

### Race Conditions
```python
import threading

counter = 0
lock = threading.Lock()

def increment():
    global counter
    with lock:
        current = counter
        time.sleep(0.1)  # Simulate work
        counter = current + 1
```

### Deadlock Prevention
```python
def transfer_money(account1, account2, amount):
    # Always acquire locks in the same order
    first, second = sorted([account1, account2])
    with first.lock:
        with second.lock:
            account1.balance -= amount
            account2.balance += amount
```

### Atomic Operations
```python
from threading import Lock
from contextlib import contextmanager

@contextmanager
def atomic_operation(lock):
    with lock:
        try:
            yield
        except Exception:
            # Rollback changes if needed
            raise
```

## Common Patterns

### Producer-Consumer
```python
import queue
import threading

q = queue.Queue()

def producer():
    for i in range(5):
        q.put(i)
        print(f"Produced: {i}")

def consumer():
    while True:
        item = q.get()
        if item is None:
            break
        print(f"Consumed: {item}")
        q.task_done()

# Create and start threads
prod = threading.Thread(target=producer)
cons = threading.Thread(target=consumer)
prod.start()
cons.start()
```

### Thread Pool
```python
from concurrent.futures import ThreadPoolExecutor

def worker(x):
    return x * x

with ThreadPoolExecutor(max_workers=3) as executor:
    results = list(executor.map(worker, range(10)))
```

### Thread-safe Singleton
```python
from threading import Lock

class Singleton:
    _instance = None
    _lock = Lock()

    def __new__(cls):
        if cls._instance is None:
            with cls._lock:
                if cls._instance is None:
                    cls._instance = super().__new__(cls)
        return cls._instance
```

## Best Practices

1. **Thread Management**
   - Use thread pools for multiple tasks
   - Implement proper thread cleanup
   - Monitor thread resources

2. **Synchronization**
   - Use appropriate primitives
   - Avoid nested locks
   - Implement timeout mechanisms

3. **Error Handling**
   - Handle thread exceptions
   - Implement proper cleanup
   - Use context managers

4. **Performance**
   - Consider GIL limitations
   - Use thread pools effectively
   - Monitor thread overhead

- [Async Programming](../advanced/async.md)
- Concurrency Patterns
- Performance Optimization
- Debugging 

Next: [Threading (Guide)](../threading/threading.md)
