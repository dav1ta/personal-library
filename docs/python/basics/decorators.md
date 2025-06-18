# Decorators in Python

Decorators are functions that modify the behavior of other functions or classes.

## Basic Function Decorator
```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("Before function call")
        result = func(*args, **kwargs)
        print("After function call")
        return result
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")

say_hello()
```

## Built-in Decorators
- `@staticmethod`
- `@classmethod`
- `@property`

## Example: Timing Decorator
```python
import time

def timer(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"Elapsed: {end - start}s")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(1)

slow_function()
```

## Resources
- [Python decorators](https://realpython.com/primer-on-python-decorators/) 