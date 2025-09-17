
```python
@dataclass
class Serializer:
    def __init__(self,dict_values):
        self.values = dict_values


    def serialize(self,object):
        return [ trans(getattr(object,field))for field,trans in self.values.items()]




class Serialize:

    def __init__(self,**trans) -> None:
        self.serializer = Serializer(trans)


    def __call__(self,object):
        print(object) ## Event

        def wrapper(instance):# Intance
            return self.serializer.serialize(instance)

        object.serialize=wrapper

        return object



def serialize(**trans):
    serializer = Serializer(trans)
    def wrapper(class_obj):
        def inner(instance):
            return  serializer.serialize(instance)
        class_obj.serialize=inner
        return class_obj
    return wrapper




@serialize(username=str,password=str,ip=str)
@dataclass
class Event:
    username:int
    password:int
    ip:int


x=Event(11,33,444)

print(x.serialize())
```

## wrapper coroutines

```python
import inspect
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

## extended syntax for decorators

```python
def _log(f, *args, **kwargs):
    print(f"calling {f.__qualname__!r} with {args=} and {kwargs=}")
    return f(*args, **kwargs)

@(lambda f: lambda *args, **kwargs: _log(f, *args, **kwargs))
def func(x):
  return x + 1
```

## same decorator for function and class


```python
from functools import wraps

from types import MethodType


class inject_db_driver:

    def __init__(self,function):
        self.function = function
        wraps(self.function)(self)


    def __call__(self,dbstring):
        print(dbstring)
        return self.function(lambda dbstring: dbstring)

    def __get__(self, instance,owner):
        print("dd")
        if instance is None:
            return self

        print(MethodType(self.function,instance))

        return self.__class__(MethodType(self.function,instance))


@inject_db_driver
def run_query(driver):
    return "test"


class DataHandler:
    @inject_db_driver
    def run_query(self,driver):
        return "test"


# run_query("dato")

x=DataHandler()
x.run_query("dato")
```



## composition over inheritance


```python
from dataclasses import dataclass
class BaseResolverMixin:
    def __getattr__(self, attr: str):
        if attr.startswith("resolve_"):
            *_, actual_attr = attr.partition("resolve_")
        else:
            actual_attr = attr
        try:
            return self.__dict__[actual_attr]
        except KeyError as e:
            raise AttributeError from e
@dataclass
class Customer(BaseResolverMixin):
    customer_id: str
    name: str
    address: str




#######

def _resolver_method(self, attr):
    if attr.startswith("resolve_"):
        *_, actual_attr = attr.partition("resolve_")
    else:
        actual_attr = attr
    try:
        return self.__dict__[actual_attr]
    except KeyError as e:
        raise AttributeError from e



def with_resolver(cls):
    cls.__getattr__=_resolver_method
    return cls


@dataclass
@with_resolver
class Customer(BaseResolverMixin):
    customer_id: str
    name: str
    address: str
```
> Note: See `advanced/decorators.md` for the canonical, in-depth guide to decorators. This page contains snippet-style examples.
