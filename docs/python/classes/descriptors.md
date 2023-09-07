```python
class DescriptorClass:
    def __get__(self, instance, owner):
        if instance is None:
            return self
        print(
        self.__class__.__name__,
        instance,
        owner)
        return instance

class ClientClass:
    descriptor = DescriptorClass()

client = ClientClass()
client.descriptor
```

## Descriptor Methods

- `__get__(self, instance, owner)`: The `__get__` method of the descriptor class. It takes three arguments: `self`, `instance` (where the descriptor is called from), and `owner` (a reference to the class object). `owner` is the same as `instance.__class__`.

- `__set__(self, instance, value)`: The `__set__` method of the descriptor class. It is called when assigning a value to the descriptor. Example usage: `client.descriptor = 'value'`.

- `__delete__(self, instance)`: The `__delete__` method of the descriptor class. It is called when deleting the descriptor. Example usage: `del client.descriptor`.

- `__set_name__(self, owner, name)`: The `__set_name__` method of the descriptor class. It is called during the class creation and provides the field name.

```python
class DescriptorWithName:
    def __init__(self, name):
        self.name = name
    def __get__(self, instance, value):
        if instance is None:
            return self
        print(self.name, instance)
        return instance.__dict__[self.name]
    def __set__(self, instance, value):
        instance.__dict__[self.name] = value

class ClientClass:
    descriptor = DescriptorWithName("descriptor")
```

## Descriptor Types

- Non-data descriptor: Implements only the `__get__` method.

- Data descriptor: Implements both the `__get__` and `__set__` methods.

Why is it accessing the `__dict__` attribute of the instance directly? Another good question, which also has at least two explanations. First, you might be thinking why not just do the following? `setattr(instance, "descriptor", value)`

Remember that this method (`__set__`) is called when we try to assign something to the attribute that is a descriptor. So, using `setattr()` will call this descriptor again, which, in turn, will call it again, and so on and so forth. This will end up in an infinite recursion.

Why, then, is the descriptor not able to book-keep the values of the properties for all of its objects? The client class already has a reference to the descriptor. If we add a reference from the descriptor back to the client object, we are creating circular dependencies, and these objects will never be garbage-collected. Since they are pointing at each other, their reference counts will never drop below the threshold for removal, and that will cause memory leaks in our program.

A possible alternative here is to use weak references, with the `weakref` module, and create a weak reference key dictionary if we want to do that. This implementation is explained later on in this chapter, but for the implementations within this book, we prefer to use this idiom (and not `weakref`), since it is fairly common and accepted when writing descriptors. As of now, we have studied the different kinds of descriptors, what they are, and how they work, and we even got a first idea of how we can use them to our advantage. The next section emphasizes precisely that last point: we'll see descriptors in action. From now on, we'll take a more practical approach, and see how we can use descriptors to achieve better code. After that, we'll even explore examples of good descriptors.

## Functions and Methods

The most resonating case of an object that is a descriptor is probably a function. Functions implement the `__get__` method, so they can work as methods when defined inside a class. In Python, methods are just regular functions, only they take an extra argument. By convention, the first argument of a method is named `self`, and it represents an instance of the class that the method is being defined in. Then, whatever the method does with `self` would be the same as any other function receiving the object and applying modifications to it. In other words, when we define something like this:

```python
class MyClass:
    def method(self, ...):
        self.x = 1
```

Since functions implement the descriptor protocol, before calling the method, the `__get__` method is invoked first. Then, within this `__get__` method, some transformations happen before running the code on the internal callable.

### Function as Descriptor

```python
from types import MethodType

class Method:
    def __init__(self, name):
        self.name = name
    def __call__(self, instance, arg1, arg2):
        print(f"{self.name}: {instance} called with {arg1} and {arg2}")
    def __get__(self, instance, owner):
        if instance is None:
            return self
        return MethodType(self, instance)
```

Since this is a very elegant solution, it's worth exploring it to keep it in mind as a Pythonic approach when defining our own objects. For instance, if we were to define our own callable, it would be a good idea to also make it a descriptor so that we can use it in classes as class attributes as well.
```

