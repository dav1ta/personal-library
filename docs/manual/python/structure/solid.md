# SOLID Principles in Python with Examples

SOLID is an acronym representing five design principles intended to make software designs more understandable, flexible, and maintainable. Here's a brief overview and examples in Python for each principle.

## 1. Single Responsibility Principle (SRP)
A class should have only one reason to change, meaning it should have only one job.

```python
class Order:
    def __init__(self):
        self.items = []
        self.quantities = []
        self.prices = []
        self.status = "open"

    def add_item(self, item, quantity, price):
        self.items.append(item)
        self.quantities.append(quantity)
        self.prices.append(price)

# SRP Violation: Adding order processing logic to Order class
# Solution: Separate order processing into another class
class OrderProcessor:
    def process_order(self, order):
        if order.status == "open":
            # Process the order
            order.status = "closed"
            print("Order processed.")
```

## 2. Open/Closed Principle (OCP)
Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.

```python
class Discount:
    def __init__(self, customer, price):
        self.customer = customer
        self.price = price

    def give_discount(self):
        if self.customer == "fav":
            return self.price * 0.2
        if self.customer == "vip":
            return self.price * 0.4

# OCP Violation: Modifying Discount class each time to add a new customer type
# Solution: Extend Discount class without modifying it
class VIPDiscount(Discount):
    def give_discount(self):
        return super().give_discount() * 1.2
```

## 3. Liskov Substitution Principle (LSP)
Objects of a superclass shall be replaceable with objects of its subclasses without affecting the correctness of the program.

```python
class Bird:
    def fly(self):
        pass

class Duck(Bird):
    def fly(self):
        print("Duck flying")

class Ostrich(Bird):
    def fly(self):
        raise NotImplementedError("Ostrich cannot fly")

# LSP Violation: Ostrich is a Bird but cannot fly
# Solution: Introduce a new class hierarchy
class FlyingBird(Bird):
    def fly(self):
        pass

class NonFlyingBird(Bird):
    pass
```

## 4. Interface Segregation Principle (ISP)
No client should be forced to depend on methods it does not use.

```python
from abc import ABC, abstractmethod

class Machine(ABC):
    @abstractmethod
    def print(self):
        pass

    @abstractmethod
    def scan(self):
        pass

# ISP Violation: A simple printer class forced to implement scan method
# Solution: Split the interface
class Printer(ABC):
    @abstractmethod
    def print(self):
        pass

class Scanner(ABC):
    @abstractmethod
    def scan(self):
        pass
```

## 5. Dependency Inversion Principle (DIP)
High-level modules should not depend on low-level modules. Both should depend on abstractions. Moreover, abstractions should not depend on details. Details should depend on abstractions.

```python
from abc import ABC, abstractmethod

class Button:
    def __init__(self, lamp):
        self.lamp = lamp

    def toggle(self):
        if self.lamp.is_on():
            self.lamp.turn_off()
        else:
            self.lamp.turn_on()

# DIP Violation: Button class directly depends on a specific Lamp class
# Solution: Use an interface to invert the dependency
class Switchable(ABC):
    @abstractmethod
    def turn_on(self):
        pass

    @abstractmethod
    def turn_off(self):
        pass
```

By adhering to these principles, developers can create more maintainable, scalable, and robust systems.

Next: [Structure](structure.md)
