# SOLID Principles in Python

## Introduction

SOLID is an acronym representing five design principles that help create more maintainable, flexible, and robust software. These principles are fundamental to object-oriented design and are particularly relevant in Python development.

## Single Responsibility Principle (SRP)

A class should have only one reason to change, meaning it should have only one job or responsibility.

### Example
```python
# Violation of SRP
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

    def process_order(self):  # This violates SRP
        if self.status == "open":
            # Process the order
            self.status = "closed"
            print("Order processed.")

# Following SRP
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

class OrderProcessor:
    def process_order(self, order):
        if order.status == "open":
            # Process the order
            order.status = "closed"
            print("Order processed.")
```

### Benefits
- Easier to maintain
- Better code organization
- Improved testability
- Reduced coupling

## Open/Closed Principle (OCP)

Software entities should be open for extension but closed for modification.

### Example
```python
# Violation of OCP
class Discount:
    def __init__(self, customer, price):
        self.customer = customer
        self.price = price

    def give_discount(self):
        if self.customer == "fav":
            return self.price * 0.2
        if self.customer == "vip":
            return self.price * 0.4
        # Adding new customer types requires modification

# Following OCP
class Discount:
    def __init__(self, customer, price):
        self.customer = customer
        self.price = price

    def give_discount(self):
        return self.price * self.get_discount_rate()

    def get_discount_rate(self):
        return 0.0

class VIPDiscount(Discount):
    def get_discount_rate(self):
        return 0.4

class FavDiscount(Discount):
    def get_discount_rate(self):
        return 0.2
```

### Benefits
- Reduced risk of breaking existing code
- Easier to add new features
- Better code reuse
- More maintainable codebase

## Liskov Substitution Principle (LSP)

Objects of a superclass should be replaceable with objects of its subclasses without breaking the application.

### Example
```python
# Violation of LSP
class Bird:
    def fly(self):
        pass

class Duck(Bird):
    def fly(self):
        print("Duck flying")

class Ostrich(Bird):
    def fly(self):
        raise NotImplementedError("Ostrich cannot fly")

# Following LSP
class Bird:
    def move(self):
        pass

class FlyingBird(Bird):
    def fly(self):
        print("Bird flying")

class NonFlyingBird(Bird):
    def walk(self):
        print("Bird walking")
```

### Benefits
- Type safety
- Better polymorphism
- Easier to maintain
- More predictable behavior

## Interface Segregation Principle (ISP)

Clients should not be forced to depend on interfaces they do not use.

### Example
```python
# Violation of ISP
from abc import ABC, abstractmethod

class Machine(ABC):
    @abstractmethod
    def print(self):
        pass

    @abstractmethod
    def scan(self):
        pass

    @abstractmethod
    def fax(self):
        pass

# Following ISP
class Printer(ABC):
    @abstractmethod
    def print(self):
        pass

class Scanner(ABC):
    @abstractmethod
    def scan(self):
        pass

class Fax(ABC):
    @abstractmethod
    def fax(self):
        pass

class MultiFunctionPrinter(Printer, Scanner, Fax):
    def print(self):
        print("Printing...")

    def scan(self):
        print("Scanning...")

    def fax(self):
        print("Faxing...")
```

### Benefits
- More focused interfaces
- Better code organization
- Reduced coupling
- Easier to maintain

## Dependency Inversion Principle (DIP)

High-level modules should not depend on low-level modules. Both should depend on abstractions.

### Example
```python
# Violation of DIP
class Button:
    def __init__(self, lamp):
        self.lamp = lamp

    def toggle(self):
        if self.lamp.is_on():
            self.lamp.turn_off()
        else:
            self.lamp.turn_on()

# Following DIP
from abc import ABC, abstractmethod

class Switchable(ABC):
    @abstractmethod
    def turn_on(self):
        pass

    @abstractmethod
    def turn_off(self):
        pass

class Button:
    def __init__(self, device: Switchable):
        self.device = device

    def toggle(self):
        if self.device.is_on():
            self.device.turn_off()
        else:
            self.device.turn_on()
```

### Benefits
- Reduced coupling
- Better testability
- More flexible code
- Easier to maintain

## Best Practices

1. Keep classes focused and small
2. Use inheritance carefully
3. Design interfaces for specific clients
4. Depend on abstractions
5. Write tests for your code
6. Document design decisions
7. Review code regularly
8. Refactor when necessary

## Common Pitfalls

1. Over-engineering
2. Premature abstraction
3. Violating LSP
4. Creating fat interfaces
5. Tight coupling
6. Ignoring SRP
7. Modifying existing code
8. Not using abstractions

- Design Patterns
- [Code Design](code_design.md)
- Testing
- Refactoring 

Next: [Best Practices](best_practices.md)
