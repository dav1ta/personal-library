# Python Descriptors

This guide covers Python descriptors, their types, and how to use them effectively.

## Table of Contents
- [Introduction](#introduction)
- [Descriptor Protocol](#descriptor-protocol)
- [Descriptor Types](#descriptor-types)
- [Common Patterns](#common-patterns)
- [Best Practices](#best-practices)

## Introduction

### What are Descriptors?
Descriptors are objects that define how attributes are accessed, set, or deleted. They provide a way to customize attribute access and implement reusable behavior.

### Key Concepts
- Attribute access control
- Reusable behavior
- Method binding
- Property-like functionality

## Descriptor Protocol

### Basic Descriptor
```python
class Descriptor:
    def __get__(self, instance, owner):
        if instance is None:
            return self
        print(f"Getting {self.__class__.__name__}")
        return instance._value

    def __set__(self, instance, value):
        print(f"Setting {self.__class__.__name__}")
        instance._value = value

    def __delete__(self, instance):
        print(f"Deleting {self.__class__.__name__}")
        del instance._value
```

### Descriptor with Name
```python
class NamedDescriptor:
    def __init__(self, name=None):
        self.name = name

    def __set_name__(self, owner, name):
        self.name = name

    def __get__(self, instance, owner):
        if instance is None:
            return self
        return instance.__dict__[self.name]

    def __set__(self, instance, value):
        instance.__dict__[self.name] = value
```

## Descriptor Types

### Non-data Descriptor
```python
class NonDataDescriptor:
    def __get__(self, instance, owner):
        if instance is None:
            return self
        return "Non-data descriptor value"
```

### Data Descriptor
```python
class DataDescriptor:
    def __get__(self, instance, owner):
        if instance is None:
            return self
        return instance.__dict__.get(self.name)

    def __set__(self, instance, value):
        instance.__dict__[self.name] = value
```

### Lazy Property
```python
class LazyProperty:
    def __init__(self, function):
        self.function = function
        self.name = function.__name__

    def __get__(self, instance, owner):
        if instance is None:
            return self
        value = self.function(instance)
        setattr(instance, self.name, value)
        return value
```

## Common Patterns

### Validated Property
```python
class ValidatedProperty:
    def __init__(self, name, validator):
        self.name = name
        self.validator = validator

    def __get__(self, instance, owner):
        if instance is None:
            return self
        return instance.__dict__[self.name]

    def __set__(self, instance, value):
        if not self.validator(value):
            raise ValueError(f"Invalid value: {value}")
        instance.__dict__[self.name] = value
```

### Cached Property
```python
class CachedProperty:
    def __init__(self, function):
        self.function = function
        self.name = function.__name__

    def __get__(self, instance, owner):
        if instance is None:
            return self
        if self.name not in instance.__dict__:
            instance.__dict__[self.name] = self.function(instance)
        return instance.__dict__[self.name]
```

### Method Binding
```python
from types import MethodType

class Method:
    def __init__(self, name):
        self.name = name

    def __call__(self, instance, *args, **kwargs):
        print(f"{self.name}: {instance} called with {args} and {kwargs}")

    def __get__(self, instance, owner):
        if instance is None:
            return self
        return MethodType(self, instance)
```

## Best Practices

1. **Descriptor Design**
   - Keep descriptors focused and single-purpose
   - Use clear and descriptive names
   - Document behavior clearly

2. **Performance**
   - Avoid unnecessary attribute access
   - Use appropriate caching strategies
   - Consider memory usage

3. **Error Handling**
   - Provide clear error messages
   - Handle edge cases
   - Validate input appropriately

4. **Code Organization**
   - Group related descriptors
   - Maintain consistent style
   - Use type hints

- [Data Model](../advanced/data_model.md)
- Properties
- Metaclasses
- [Class Decorators](../advanced/decorators.md) 