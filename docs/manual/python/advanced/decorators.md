# Python Decorators

## Introduction

Decorators are a powerful feature in Python that allows you to modify the behavior of functions and classes. This guide covers various types of decorators and their applications.

## Basic Decorators

### Function Decorators
```python
def timing(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        latency = time.time() - start
        return {"latency": latency, "result": result}
    return wrapper

@timing
def my_function():
    pass
```

### Class Decorators
```python
def singleton(cls):
    instances = {}
    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]
    return get_instance

@singleton
class MyClass:
    pass
```

## Advanced Decorator Patterns

### Decorator with Arguments
```python
def repeat(times):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(times=3)
def greet(name):
    print(f"Hello, {name}!")
```

### Method Decorators
```python
class inject_db_driver:
    def __init__(self, function):
        self.function = function
        wraps(self.function)(self)

    def __call__(self, dbstring):
        return self.function(lambda dbstring: dbstring)

    def __get__(self, instance, owner):
        if instance is None:
            return self
        return self.__class__(MethodType(self.function, instance))

class DataHandler:
    @inject_db_driver
    def run_query(self, driver):
        return "test"
```

### Coroutine Decorators
```python
def timing(callable):
    @wraps(callable)
    def wrapped(*args, **kwargs):
        start = time.time()
        result = callable(*args, **kwargs)
        latency = time.time() - start
        return {"latency": latency, "result": result}

    @wraps(callable)
    async def wrapped_coro(*args, **kwargs):
        start = time.time()
        result = await callable(*args, **kwargs)
        latency = time.time() - start
        return {"latency": latency, "result": result}

    if inspect.iscoroutinefunction(callable):
        return wrapped_coro
    return wrapped
```

## Decorator Composition

### Multiple Decorators
```python
@decorator1
@decorator2
@decorator3
def function():
    pass
```

### Decorator Factory
```python
def _log(f, *args, **kwargs):
    print(f"calling {f.__qualname__!r} with {args=} and {kwargs=}")
    return f(*args, **kwargs)

@(lambda f: lambda *args, **kwargs: _log(f, *args, **kwargs))
def func(x):
    return x + 1
```

## Practical Examples

### Serialization Decorator
```python
@dataclass
class Serializer:
    def __init__(self, dict_values):
        self.values = dict_values

    def serialize(self, object):
        return [trans(getattr(object, field)) 
                for field, trans in self.values.items()]

def serialize(**trans):
    serializer = Serializer(trans)
    def wrapper(class_obj):
        def inner(instance):
            return serializer.serialize(instance)
        class_obj.serialize = inner
        return class_obj
    return wrapper

@serialize(username=str, password=str, ip=str)
@dataclass
class Event:
    username: int
    password: int
    ip: int
```

### Resolver Mixin
```python
def with_resolver(cls):
    def _resolver_method(self, attr):
        if attr.startswith("resolve_"):
            *_, actual_attr = attr.partition("resolve_")
        else:
            actual_attr = attr
        try:
            return self.__dict__[actual_attr]
        except KeyError as e:
            raise AttributeError from e

    cls.__getattr__ = _resolver_method
    return cls

@dataclass
@with_resolver
class Customer:
    customer_id: str
    name: str
    address: str
```

## Best Practices

1. Always use `@wraps` to preserve function metadata
2. Keep decorators simple and focused
3. Use decorator factories for configurable decorators
4. Consider using classes for complex decorators
5. Document decorator behavior and requirements
6. Handle both synchronous and asynchronous functions
7. Use type hints for better code clarity