# Advanced Python Classes and OOP

This guide covers advanced Python class concepts and object-oriented programming patterns.

## Table of Contents
- [Introduction](#introduction)
- [Class Design](#class-design)
- [Inheritance and Composition](#inheritance-and-composition)
- [Special Methods](#special-methods)
- [Class Patterns](#class-patterns)
- [Best Practices](#best-practices)

## Introduction

### What are Classes?
Classes in Python are used to create new types of objects, encapsulating data and behavior. They provide a way to organize code and create reusable components.

### Key Concepts
- Encapsulation
- Inheritance
- Polymorphism
- Abstraction

## Class Design

### Basic Class Structure
```python
class Account:
    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance

    def deposit(self, amount):
        self.balance += amount

    def withdraw(self, amount):
        self.balance -= amount
```

### Class Variables vs Instance Variables
```python
class Account:
    num_accounts = 0  # Class variable

    def __init__(self, owner, balance):
        self.owner = owner      # Instance variable
        self.balance = balance  # Instance variable
        Account.num_accounts += 1
```

## Inheritance and Composition

### Inheritance
```python
class Animal:
    def speak(self):
        raise NotImplementedError

class Dog(Animal):
    def speak(self):
        return "Woof!"
```

### Composition
```python
class Stack:
    def __init__(self):
        self._items = list()

    def push(self, item):
        self._items.append(item)

    def pop(self):
        return self._items.pop()

    def __len__(self):
        return len(self._items)
```

### Dependency Injection
```python
class Stack:
    def __init__(self, *, container=None):
        if container is None:
            container = list()
        self._items = container

    def push(self, item):
        self._items.append(item)

    def pop(self):
        return self._items.pop()
```

## Special Methods

### Basic Special Methods
```python
class Account:
    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance

    def __repr__(self):
        return f'{type(self).__name__}({self.owner!r}, {self.balance!r})'

    def __str__(self):
        return f'Account of {self.owner} with balance {self.balance}'
```

### Container Methods
```python
class AccountPortfolio:
    def __init__(self):
        self.accounts = []

    def __len__(self):
        return len(self.accounts)

    def __getitem__(self, index):
        return self.accounts[index]

    def __iter__(self):
        return iter(self.accounts)
```

### Context Manager Methods
```python
class DatabaseConnection:
    def __init__(self, host, port):
        self.host = host
        self.port = port
        self.connection = None

    def __enter__(self):
        self.connection = connect(self.host, self.port)
        return self.connection

    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.connection:
            self.connection.close()
```

## Class Patterns

### Factory Pattern
```python
class AnimalFactory:
    @staticmethod
    def create_animal(animal_type):
        if animal_type == "dog":
            return Dog()
        elif animal_type == "cat":
            return Cat()
        raise ValueError(f"Unknown animal type: {animal_type}")
```

### Singleton Pattern
```python
class Singleton:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
```

### Observer Pattern
```python
class Subject:
    def __init__(self):
        self._observers = []

    def attach(self, observer):
        self._observers.append(observer)

    def notify(self):
        for observer in self._observers:
            observer.update()
```

## Best Practices

1. **Composition Over Inheritance**
   - Prefer composition for code reuse
   - Use inheritance for "is-a" relationships
   - Avoid deep inheritance hierarchies

2. **Encapsulation**
   - Use private attributes with `_` prefix
   - Provide public methods for access
   - Use properties for controlled access

3. **Interface Design**
   - Keep interfaces small and focused
   - Use abstract base classes for interfaces
   - Document expected behavior

4. **Error Handling**
   - Use custom exceptions
   - Provide meaningful error messages
   - Handle errors at appropriate levels

- [Data Model](../advanced/data_model.md)
- [Descriptors](../advanced/descriptors.md)
- [Iterators](../advanced/iterators.md)
- Metaclasses 