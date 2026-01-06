
> See also: the comprehensive guide in `advanced/async.md`.

### 1. Introduction to Asynchronous Programming

Asynchronous programming is a method that allows for the execution of certain tasks concurrently without blocking the main thread. Instead of waiting for one task to complete before moving on to the next, asynchronous programming allows multiple tasks to run in "parallel", making better use of system resources and often speeding up overall execution.

**Next topic:** Traditional Multi-threading vs Asynchronous Programming.



### 2. Traditional Multi-threading vs Asynchronous Programming

In traditional multi-threading, multiple threads run in parallel. Each thread might be executing a different task or function. While this allows for concurrent execution, it also introduces complexity with thread management, synchronization, and potential deadlocks.

In contrast, asynchronous programming, especially in Python's context, utilizes a single-threaded event loop. Tasks are executed in this loop but can yield control back to the loop when waiting for some I/O operations, allowing other tasks to run.

**Advantages of Asynchronous Programming:**
- **Scalability:** Asynchronous programs can handle many tasks with a single thread.
- **Simplicity:** Avoids complexities of thread synchronization and deadlocks.

**Next topic:** Python's `asyncio` Basics.


### 3. Python's `asyncio` Basics

#### 3.1. `async` & `await`

To mark a function as asynchronous, you use the `async` keyword before `def`:
```python
async def my_async_function():
    pass
```

To call asynchronous functions or to execute asynchronous code inside an async function, you use the `await` keyword:
```python
async def fetch_data():
    data = await get_data_from_source()
    return data
```

#### 3.2. Event Loop

The event loop is the heart of every asyncio application. It allows you to schedule asynchronous tasks and callbacks, run them, and manage their execution flow.

```python
import asyncio

async def main():
    print("Hello")
    await asyncio.sleep(1)
    print("World")

asyncio.run(main())
```

#### 3.3. Tasks and Coroutines

Tasks are used to schedule coroutines concurrently. A coroutine is a special type of function that can yield control back to the event loop, allowing other coroutines to run.

```python
import asyncio

async def say_hello():
    await asyncio.sleep(1)
    print("Hello")

async def say_world():
    print("World")

async def main():
    task1 = asyncio.create_task(say_hello())
    task2 = asyncio.create_task(say_world())
    await task1
    await task2

asyncio.run(main())
```

**Next topic:** Asynchronous I/O with Python.


### 4. Asynchronous I/O with Python

One of the primary uses for asynchronous programming is handling Input/Output (I/O) operations without blocking. I/O-bound tasks, such as network requests or reading and writing to databases, often involve waiting. Asynchronous I/O lets us perform these tasks more efficiently.

For instance, when fetching data from multiple URLs, instead of waiting for each request to complete one after another, you can fetch from multiple URLs "at the same time".

```python
import aiohttp
import asyncio

async def fetch_url(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.text()

async def main():
    urls = ["http://example.com", "http://example.org", "http://example.net"]
    tasks = [fetch_url(url) for url in urls]
    results = await asyncio.gather(*tasks)
    for url, result in zip(urls, results):
        print(f"Data from {url[:30]}: {len(result)} characters")

asyncio.run(main())
```

**Next topic:** Advanced Techniques in Asynchronous Programming.


### 5. Advanced Techniques in Asynchronous Programming

#### 5.1. Managing Multiple Tasks with `gather` & `wait`

We've already seen `gather` in action, which waits for all tasks to complete and returns their results. However, sometimes you might want to proceed as soon as one of the tasks completes, and for that, you can use `asyncio.wait` with the `FIRST_COMPLETED` option.

```python
import asyncio

async def task_one():
    await asyncio.sleep(2)
    return "Task One Completed!"

async def task_two():
    await asyncio.sleep(1)
    return "Task Two Completed!"

async def main():
    tasks = [task_one(), task_two()]
    done, pending = await asyncio.wait(tasks, return_when=asyncio.FIRST_COMPLETED)

    for task in done:
        print(task.result())

asyncio.run(main())
```

#### 5.2. Handling Timeouts and Delays

Sometimes you might not want to wait indefinitely for a task to complete. Using `asyncio.wait_for`, you can set a timeout.

```python
import asyncio

async def long_task():
    await asyncio.sleep(10)

async def main():
    try:
        await asyncio.wait_for(long_task(), timeout=5)
    except asyncio.TimeoutError:
        print("Task took too long!")

asyncio.run(main())
```

#### 5.3. Error Handling in Async Context

Just like with synchronous code, you can use try-except blocks to handle errors in asynchronous functions.

```python
import asyncio

async def risky_task():
    raise ValueError("This is an intentional error!")

async def main():
    try:
        await risky_task()
    except ValueError as e:
        print(f"Caught an error: {e}")

asyncio.run(main())
```

**Next topic:** Integration with Other Libraries.
### 6. Integration with Other Libraries

#### 6.1. `aiohttp` for Asynchronous HTTP Requests

We briefly touched on `aiohttp` earlier. It's a powerful library that provides asynchronous HTTP client and server functionality. The client lets you make non-blocking requests, while the server allows you to handle incoming requests asynchronously.

Example using `aiohttp` as a server:

```python
from aiohttp import web

async def handle(request):
    return web.Response(text="Hello, world!")

app = web.Application()
app.router.add_get('/', handle)

web.run_app(app)
```

#### 6.2. `aiomysql` & `aiopg` for Asynchronous Database Operations

For database operations, you can use libraries like `aiomysql` for MySQL and `aiopg` for PostgreSQL. These libraries provide asynchronous interfaces to interact with databases.

Example using `aiomysql`:

```python
import asyncio
import aiomysql

async def main():
    pool = await aiomysql.create_pool(host='127.0.0.1', port=3306, user='user', password='password', db='testdb')

    async with pool.acquire() as conn:
        async with conn.cursor() as cur:
            await cur.execute("SELECT some_column FROM some_table;")
            print(await cur.fetchone())

    pool.close()
    await pool.wait_closed()

asyncio.run(main())
```

**Next topic:** Potential Pitfalls and Common Mistakes.
### 7. Potential Pitfalls and Common Mistakes

Understanding the potential pitfalls in asynchronous programming can save developers a lot of time and prevent unexpected behaviors.

#### 7.1. Mixing Sync and Async Code

One of the common mistakes is mixing synchronous code with asynchronous code without being aware of the consequences. For instance, using a blocking function inside an async function can halt the entire event loop.

```python
import asyncio
import time

async def wrong_usage():
    time.sleep(3)  # This is a blocking call
    print("This will block the entire event loop")

asyncio.run(wrong_usage())
```

Always ensure that you're using non-blocking alternatives inside async functions.

#### 7.2. Forgetting `await`

Another easy mistake is forgetting the `await` keyword when calling an async function. This results in the function not being executed, and instead, a coroutine object is returned.

```python
async def greet():
    return "Hello, World!"

async def main():
    greeting = greet()  # Forgot await
    print(greeting)  # This will print a coroutine object, not the greeting.

asyncio.run(main())
```

#### 7.3. Not Handling Exceptions in Tasks

If an exception is raised in a Task and not caught, it won't propagate immediately. Instead, it will propagate when the Task object is garbage collected, which can make debugging tricky.

```python
import asyncio

async def raise_error():
    raise Exception("Intentional Error")

async def main():
    task = asyncio.create_task(raise_error())
    await asyncio.sleep(1)

asyncio.run(main())
```

Always ensure you handle exceptions in your tasks, either within the task or when gathering/waiting for them.

**Next topic:** Best Practices & Recommendations.
### 8. Best Practices & Recommendations

When writing asynchronous code, following best practices can help maintainability, performance, and overall code quality.

#### 8.1. Use `async` and `await` Consistently

Ensure that you're consistently using the `async` and `await` keywords appropriately. If a function is asynchronous, mark it with `async` and ensure that its callers are aware that they're calling an async function.

#### 8.2. Favor High-Level APIs

Python's `asyncio` provides both high-level and low-level APIs. Whenever possible, favor high-level APIs as they are more user-friendly and abstract away a lot of the complexity.

#### 8.3. Use Asynchronous Context Managers

Many async libraries provide asynchronous context managers, which help in ensuring that resources are properly managed. 

For example, with `aiohttp`, you can use:

```python
async with aiohttp.ClientSession() as session:
    ...
```

This ensures that the session is properly closed after usage.

#### 8.4. Be Wary of Thread-Safety

Even though asynchronous code in Python usually runs in a single thread, if you integrate with other systems or use thread pools, be aware of thread-safety. Ensure shared resources are accessed in a thread-safe manner.

**Next topic:** Conclusion and Future of Python Async.
### 9. Conclusion and Future of Python Async

Asynchronous programming in Python has come a long way, especially with the introduction and continuous development of `asyncio`. It provides a powerful toolset for writing efficient I/O-bound programs.

However, like all tools, it's essential to understand its strengths and limitations, and when to use it. Not all problems are best solved with asynchronicity, and sometimes, traditional multi-threading or even multi-processing can be more appropriate.

The future looks bright for async in Python, with continuous enhancements to `asyncio` and a growing ecosystem of asynchronous libraries. As the community gains more experience and the tooling improves, we can expect even more robust and performant asynchronous applications in Python.

**End of Topics.**
### 10. Advanced Queue Operations with `asyncio`

`asyncio` provides a Queue class that is similar to `queue.Queue` but designed to be used with async functions.

#### 10.1. Basic Queue Operations

Queues are an essential part of many concurrent programs and can be used to pass messages between different parts of a system.

```python
import asyncio

async def producer(queue):
    for i in range(5):
        await queue.put(i)
        print(f"Produced {i}")
        await asyncio.sleep(1)

async def consumer(queue):
    for _ in range(5):
        item = await queue.get()
        print(f"Consumed {item}")

async def main():
    queue = asyncio.Queue()
    await asyncio.gather(producer(queue), consumer(queue))

asyncio.run(main())
```

#### 10.2. Implementing Producer-Consumer with `asyncio`

The producer-consumer pattern is a classic concurrency pattern where one or more producers add tasks to a queue, and one or more consumers take tasks from the queue and process them.

```python
import asyncio
import random

async def producer(queue, name):
    for _ in range(5):
        item = random.randint(1, 10)
        await asyncio.sleep(random.random())
        await queue.put(item)
        print(f"Producer {name} produced {item}")

async def consumer(queue, name):
    while True:
        await asyncio.sleep(random.random())
        item = await queue.get()
        if item is None:  # Sentinel value to exit
            break
        print(f"Consumer {name} consumed {item}")

async def main():
    queue = asyncio.Queue()

    consumers = [asyncio.create_task(consumer(queue, name=i)) for i in range(3)]
    producers = [asyncio.create_task(producer(queue, name=i)) for i in range(3)]

    # Wait for all producers to finish
    await asyncio.gather(*producers)

    # Signal the consumers to exit
    for _ in range(len(consumers)):
        await queue.put(None)

    # Wait for all consumers to exit
    await asyncio.gather(*consumers)

asyncio.run(main())
```

#### 10.3. Limiting Queue Size

For certain applications, you might want to limit the number of items a queue can hold. This can be useful to apply backpressure on the producer when the queue gets full.

```python
queue = asyncio.Queue(maxsize=5)

async def bounded_producer(queue):
    for i in range(10):
        print(f"Producing {i}")
        await queue.put(i)
        print(f"Produced {i}")
        await asyncio.sleep(0.5)

asyncio.run(bounded_producer(queue))
```

When the queue reaches its maximum size, `queue.put` will block until there's room to add another item.

**Next topic:** More Advanced Techniques in Asynchronous Programming.
### 11. More Advanced Techniques in Asynchronous Programming

#### 11.1. Priority Queues

You can use priority queues to ensure that some tasks get priority over others:

```python
import asyncio
import heapq

class AsyncPriorityQueue:
    def __init__(self):
        self._queue = []
        self._count = 0
        self._event = asyncio.Event()

    async def put(self, item, priority):
        heapq.heappush(self._queue, (priority, self._count, item))
        self._count += 1
        self._event.set()

    async def get(self):
        while not self._queue:
            self._event.clear()
            await self._event.wait()
        priority, count, item = heapq.heappop(self._queue)
        return item
```

#### 11.2. Semaphores and Locks

Semaphores and locks are synchronization primitives that can be used to protect resources:

```python
import asyncio

sem = asyncio.Semaphore(10)  # Allows 10 tasks to access a resource at a time

async def worker(num):
    async with sem:
        print(f"Worker {num} has started")
        await asyncio.sleep(1)
        print(f"Worker {num} has finished")

asyncio.run(asyncio.gather(*(worker(i) for i in range(20))))
```

#### 11.3. Async Streams

Async streams allow you to consume or produce multiple values with async iteration:

```python
import asyncio

async def ticker(delay, to):
    for i in range(to):
        yield i
        await asyncio.sleep(delay)

async def main():
    async for tick in ticker(1, 5):
        print(f"Tick: {tick}")

asyncio.run(main())
```

#### 11.4. Exception Propagation

When working with tasks, handling exceptions is crucial:

```python
import asyncio

async def raise_exception():
    raise ValueError("An error occurred!")

async def main():
    tasks = [raise_exception(), asyncio.sleep(1)]
    results, _ = await asyncio.wait(tasks, return_when=asyncio.FIRST_EXCEPTION)
    for task in results:
        try:
            task.result()  # Will raise the ValueError
        except ValueError as e:
            print(f"Caught an error: {e}")

asyncio.run(main())
```

#### 11.5. Using Tasks Effectively

While creating tasks is simple, managing their lifecycle and ensuring they complete without hanging your application can be tricky:

```python
import asyncio

async def do_work():
    await asyncio.sleep(2)

async def main():
    task = asyncio.create_task(do_work())
    await asyncio.sleep(1)
    print("Main work done!")
    await task  # Ensure all spawned tasks are awaited

asyncio.run(main())
```

**Next topic:** Combining Async IO with Multiprocessing.
### 12. Combining Async IO with Multiprocessing

While `asyncio` excels at I/O-bound tasks, it runs in a single thread and doesn't utilize multiple cores for CPU-bound tasks. For these tasks, you can combine `asyncio` with multiprocessing to achieve parallelism across cores.

#### 12.1. Basic Async with Multiprocessing

Here's a simple demonstration of running CPU-bound tasks in separate processes while using async for I/O:

```python
import asyncio
from concurrent.futures import ProcessPoolExecutor

def cpu_bound_task(data):
    # Simulating a CPU-bound task by calculating sum
    return sum(data)

async def main():
    data = [range(1000000) for _ in range(4)]
    with ProcessPoolExecutor() as pool:
        result = await asyncio.gather(*(loop.run_in_executor(pool, cpu_bound_task, d) for d in data))
    print(result)

loop = asyncio.get_event_loop()
loop.run_until_complete(main())
```

#### 12.2. Asynchronous Process Communication

Communicate between processes using `asyncio` and `multiprocessing`:

```python
import asyncio
import multiprocessing

def worker(q):
    for i in range(5):
        q.put(i)
        print(f"Produced {i}")

async def async_consumer(q):
    for _ in range(5):
        item = await loop.run_in_executor(None, q.get)
        print(f"Consumed {item}")

queue = multiprocessing.Queue()
process = multiprocessing.Process(target=worker, args=(queue,))

loop = asyncio.get_event_loop()
process.start()
loop.run_until_complete(async_consumer(queue))
process.join()
```

#### 12.3. Challenges and Considerations

- **Error Handling:** Ensure that exceptions in worker processes are properly propagated and handled.
- **Data Serialization:** Remember that data sent between processes needs to be serialized and deserialized, which can introduce overhead.
- **Resource Management:** Ensure all processes are cleaned up to avoid resource leaks or zombie processes.

**Next topic:** Advanced Patterns and Designs in Async Applications.
### 13. Advanced Patterns and Designs in Async Applications

#### 13.1. Event-driven Architecture

Using `asyncio`, you can build an event-driven system where components react to events rather than follow a strict sequential order:

```python
class EventBus:
    def __init__(self):
        self._listeners = {}

    def add_listener(self, event, listener):
        if event not in self._listeners:
            self._listeners[event] = []
        self._listeners[event].append(listener)

    async def emit(self, event, data):
        for listener in self._listeners.get(event, []):
            await listener(data)

async def print_on_event(data):
    print(f"Received event with data: {data}")

bus = EventBus()
bus.add_listener("data_event", print_on_event)

async def main():
    await bus.emit("data_event", "Some event data")

asyncio.run(main())
```

#### 13.2. Service Actor Pattern

In an async world, actors can be lightweight services that hold state and provide methods to act on that state:

```python
class ServiceActor:
    def __init__(self):
        self._state = 0

    async def increment(self):
        self._state += 1
        print(f"State incremented to {self._state}")

    async def decrement(self):
        self._state -= 1
        print(f"State decremented to {self._state}")

actor = ServiceActor()

async def main():
    await actor.increment()
    await actor.decrement()

asyncio.run(main())
```

#### 13.3. Reactive Extensions (RxPY with Async)

`RxPY` supports asynchronous operations and can be integrated with `asyncio` for reactive programming:

```python
import rx
from rx.scheduler.eventloop import AsyncIOScheduler
import asyncio

async def source(observer, scheduler):
    await asyncio.sleep(1, loop=scheduler.loop)
    observer.on_next(42)
    observer.on_completed()

stream = rx.create(source)
stream.subscribe(on_next=print, scheduler=AsyncIOScheduler(asyncio.get_event_loop()))

asyncio.get_event_loop().run_forever()
```

**Next topic:** Debugging and Profiling Asynchronous Python Applications.
### 14. Debugging and Profiling Asynchronous Python Applications

Debugging and profiling asynchronous applications can be different than traditional synchronous applications. Let's look into techniques and tools available for `asyncio`:

#### 14.1. Debug Mode in `asyncio`

`asyncio` provides a debug mode that can help catch common mistakes:

```python
import asyncio

async def forgot_await():
    asyncio.sleep(1)  # Missing `await`

asyncio.get_event_loop().set_debug(True)
asyncio.run(forgot_await())
```

In debug mode, the above will print a warning indicating that a coroutine has not been awaited.

#### 14.2. Logging Unclosed Resources

To help debug issues related to unclosed resources like sockets, you can enable logging:

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

This will print detailed debug information about resources that were not closed properly.

#### 14.3. Profiling with `aio-profiler`

`aio-profiler` is a tool specifically designed to profile asynchronous Python applications:

```bash
pip install aio-profiler
```

Using `aio-profiler`, you can visualize where your asynchronous application spends its time, helping optimize performance-critical sections.

#### 14.4. Debugging with IDEs

Modern IDEs, like PyCharm, have support for debugging asynchronous Python code. You can set breakpoints, inspect variable values, and step through async code just like synchronous code.

#### 14.5. Detecting Deadlocks

If your asynchronous code appears to hang, it could be due to a deadlock. This often happens when tasks are waiting for each other in a cycle. In such cases, tools like `aio-deadlock-detector` can help identify and break such cycles.

#### 14.6. Monitoring Asynchronous Tasks

Using the `asyncio.all_tasks()` function, you can monitor all running tasks. This can be useful to ensure no tasks are left dangling:

```python
import asyncio

async def example_task():
    await asyncio.sleep(1)

async def main():
    task = asyncio.create_task(example_task())
    print("Running tasks:", asyncio.all_tasks())

asyncio.run(main())
```

**Next topic:** Scaling and Deploying Asynchronous Applications.
### 15. Scaling and Deploying Asynchronous Applications

Once your asynchronous application is developed and tested, the next step is to deploy and scale it. Here are some strategies and considerations:

#### 15.1. Event Loop Implementations

While the default event loop in `asyncio` is sufficient for most tasks, there are alternative implementations like `uvloop` which can offer better performance:

```bash
pip install uvloop
```

```python
import asyncio
import uvloop

asyncio.set_event_loop_policy(uvloop.EventLoopPolicy())
```

#### 15.2. Load Balancing

Just like synchronous applications, asynchronous applications can benefit from load balancing to distribute incoming traffic among multiple instances of the application. Common load balancers like NGINX or HAProxy can be used.

#### 15.3. Distributed Systems and Microservices

When scaling applications, consider breaking them into microservices. Asynchronous communication can be established between services using message queues like RabbitMQ or Kafka.

#### 15.4. Database Connections

When using asynchronous databases, be aware of connection limits. Use connection pooling and avoid holding onto connections longer than necessary.

#### 15.5. Memory and Resource Leaks

Asynchronous applications, especially long-running ones, should be monitored for memory and resource leaks. Tools like `objgraph` or built-in Python profilers can help identify and fix such leaks.

#### 15.6. Error Monitoring and Alerting

Implement monitoring and alerting to keep an eye on exceptions and errors in production. Tools like Sentry can be integrated to capture and notify about runtime errors.

**Next topic:** Conclusion and Continuous Learning in Asynchronous Programming.
### 16. Conclusion and Continuous Learning in Asynchronous Programming

The landscape of asynchronous programming in Python is vast and continuously evolving. With tools like `asyncio` and the expanding ecosystem around it, developers have powerful mechanisms to write efficient, scalable, and maintainable applications.

However, the journey doesn't end with mastering `asyncio` or any specific library. The Python community is vibrant and always innovating. It's essential to stay updated, participate in discussions, and continuously experiment with new techniques, tools, and best practices.

Asynchronous programming, once an advanced topic, is slowly becoming a core skill for Python developers. Embrace the paradigm, understand its intricacies, and leverage it to build the next generation of responsive and performant Python applications.

**End of Topics.**
## EXTRA async without asyncio


```python
import time

class Task:
    def __init__(self, gen):
        self._gen = gen
        self._wake_up_time = 0

    def run(self):
        if time.time() < self._wake_up_time:
            return False
        try:
            next(self._gen)
            return True
        except StopIteration:
            return False

    def set_wake_up_time(self, delay):
        self._wake_up_time = time.time() + delay

class Scheduler:
    def __init__(self):
        self._tasks = []

    def add_task(self, task_gen):
        self._tasks.append(Task(task_gen))

    def sleep(self, current_task, delay):
        current_task.set_wake_up_time(delay)
        self._tasks.append(current_task)

    def run(self):
        while self._tasks:
            current_task = self._tasks.pop(0)
            if not current_task.run():
                self._tasks.append(current_task)

# Global scheduler instance
scheduler = Scheduler()

def async_sleep(delay):
    yield
    scheduler.sleep(current_task, delay)
    yield

def coro1():
    print("Coroutine 1: Start")
    yield from async_sleep(2)
    print("Coroutine 1: After 2 seconds")

def coro2():
    print("Coroutine 2: Start")
    yield from async_sleep(1)
    print("Coroutine 2: After 1 second")

# Add coroutines to the scheduler and run them
scheduler.add_task(coro1())
scheduler.add_task(coro2())
scheduler.run()
```

---

## Appendix: Yield Patterns (Generators and Async Generators)

These examples show practical, easy-to-reuse patterns with `yield`, `yield from`, and async generators.

### A1. Basic generator (lazy iteration)
```python
def read_lines(path):
    with open(path, encoding="utf-8") as f:
        for line in f:
            yield line.rstrip("\n")

for line in read_lines("data.txt"):
    ...
```

### A2. Sending values into a generator (`send`)
```python
def accumulator():
    total = 0
    while True:
        x = yield total  # yield current total, receive next x
        total += x

acc = accumulator()
next(acc)            # prime: returns 0
print(acc.send(5))   # 5
print(acc.send(7))   # 12
```

### A3. Delegation with `yield from` and return values (PEP 380)
```python
def subgen():
    yield 1
    yield 2
    return 99         # becomes StopIteration.value

def outer():
    result = yield from subgen()
    yield f"subgen returned {result}"

print(list(outer()))  # [1, 2, 'subgen returned 99']
```

### A4. Generator-based context managers
```python
from contextlib import contextmanager

@contextmanager
def opened(path, mode="r", **kw):
    f = open(path, mode, **kw)
    try:
        yield f
    finally:
        f.close()

with opened("data.txt", encoding="utf-8") as f:
    for line in f: ...
```

### A5. Streaming pipelines (compose generators)
```python
def grep(lines, needle):
    for ln in lines:
        if needle in ln:
            yield ln

def lower(lines):
    for ln in lines:
        yield ln.lower()

with open("app.log", encoding="utf-8") as f:
    for ln in grep(lower(f), "error"):
        ...
```

### A6. Async generators (`async def` + `yield`)
```python
import asyncio

async def ticker(delay, count):
    for i in range(count):
        await asyncio.sleep(delay)
        yield i

async def main():
    async for t in ticker(0.5, 3):
        print(t)

asyncio.run(main())
```

### A7. Async generator cleanup (`aclose` and `finally`)
```python
import asyncio

async def stream():
    try:
        while True:
            yield await asyncio.sleep(0.1, result=42)
    finally:
        print("cleanup!")

async def main():
    agen = stream()
    print(await agen.__anext__())
    await agen.aclose()  # triggers finally

asyncio.run(main())
```

### A8. Pytest fixtures with `yield` (setup/teardown)
```python
# conftest.py
import pytest

@pytest.fixture
def resource():
    obj = acquire()
    yield obj     # test runs here
    release(obj)  # teardown always runs
```

Next: [Async Patterns](advanced/async_patterns.md)
