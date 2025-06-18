# Python Code Design Principles

## Introduction

Good code design is essential for creating maintainable, scalable, and robust Python applications. This guide covers key principles and practices that help in writing better Python code.

## Design by Contract

Design by Contract is a programming approach that enforces rules and constraints during component communication.

### Key Concepts
- **Preconditions**: Requirements that must be met before function execution
- **Postconditions**: Guarantees about the function's output
- **Invariants**: Rules that remain true throughout execution
- **Side Effects**: Changes beyond the return value

### Example
```python
from typing import List
from dataclasses import dataclass

@dataclass
class Stack:
    items: List[int]

    def push(self, item: int) -> None:
        # Precondition: item must be an integer
        assert isinstance(item, int), "Item must be an integer"
        
        # Invariant: items must be a list
        assert isinstance(self.items, list), "Items must be a list"
        
        self.items.append(item)
        
        # Postcondition: item must be at the top of the stack
        assert self.items[-1] == item, "Item not properly pushed"
```

## Defensive Programming

Defensive programming involves writing code that protects itself from invalid inputs and unexpected behavior.

### Techniques
1. **Value Substitution**
```python
import os

# Using default values
port = int(os.getenv("DPORT", "5432"))
```

2. **Error Logging**
```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def process_data(data):
    try:
        # Process data
        pass
    except Exception as e:
        logger.error(f"Error processing data: {e}", exc_info=True)
        raise
```

3. **Exception Handling**
```python
def safe_divide(a: float, b: float) -> float:
    try:
        return a / b
    except ZeroDivisionError:
        logger.error("Division by zero attempted")
        raise ValueError("Cannot divide by zero")
```

### Best Practices
- Avoid empty except blocks
- Include original exceptions
- Log errors appropriately
- Use specific exception types
- Provide meaningful error messages

## Cohesion and Coupling

### Cohesion
High cohesion means a component has a single, well-defined responsibility.

```python
# Low cohesion
class UserManager:
    def create_user(self): pass
    def send_email(self): pass
    def generate_report(self): pass

# High cohesion
class UserManager:
    def create_user(self): pass
    def update_user(self): pass
    def delete_user(self): pass

class EmailService:
    def send_email(self): pass

class ReportGenerator:
    def generate_report(self): pass
```

### Coupling
Low coupling means components have minimal dependencies.

```python
# High coupling
class OrderProcessor:
    def __init__(self):
        self.database = MySQLDatabase()
        self.payment_processor = StripePaymentProcessor()
        self.email_sender = GmailEmailSender()

# Low coupling
from abc import ABC, abstractmethod

class Database(ABC):
    @abstractmethod
    def save(self): pass

class PaymentProcessor(ABC):
    @abstractmethod
    def process(self): pass

class EmailSender(ABC):
    @abstractmethod
    def send(self): pass

class OrderProcessor:
    def __init__(self, db: Database, payment: PaymentProcessor, email: EmailSender):
        self.database = db
        self.payment_processor = payment
        self.email_sender = email
```

## DRY and OAOO

### Don't Repeat Yourself (DRY)
```python
# Violation
def calculate_area_rectangle(width, height):
    return width * height

def calculate_area_square(side):
    return side * side

# Following DRY
def calculate_area(width, height=None):
    if height is None:
        height = width
    return width * height
```

### Once and Only Once (OAOO)
```python
# Violation
class User:
    def validate_email(self, email):
        if '@' not in email:
            raise ValueError("Invalid email")

class Order:
    def validate_email(self, email):
        if '@' not in email:
            raise ValueError("Invalid email")

# Following OAOO
class EmailValidator:
    @staticmethod
    def validate(email):
        if '@' not in email:
            raise ValueError("Invalid email")
```

## YAGNI and KIS

### You Ain't Gonna Need It (YAGNI)
```python
# Violation
class User:
    def __init__(self):
        self.future_feature = None  # Not needed yet

# Following YAGNI
class User:
    def __init__(self):
        self.name = None
        self.email = None
```

### Keep It Simple (KIS)
```python
# Violation
def calculate_factorial(n):
    return reduce(lambda x, y: x * y, range(1, n + 1))

# Following KIS
def calculate_factorial(n):
    if n == 0:
        return 1
    return n * calculate_factorial(n - 1)
```

## EAFP vs LBYL

### Easier to Ask Forgiveness than Permission (EAFP)
```python
# EAFP approach
def read_file(filename):
    try:
        with open(filename) as f:
            return f.read()
    except FileNotFoundError:
        logger.error(f"File {filename} not found")
        raise
```

### Look Before You Leap (LBYL)
```python
# LBYL approach
def read_file(filename):
    if not os.path.exists(filename):
        logger.error(f"File {filename} not found")
        raise FileNotFoundError(f"File {filename} not found")
    
    with open(filename) as f:
        return f.read()
```

## Best Practices

1. Write clear and concise code
2. Use type hints
3. Document your code
4. Write tests
5. Follow PEP 8
6. Use meaningful names
7. Keep functions small
8. Handle errors gracefully

## Common Pitfalls

1. Over-engineering
2. Premature optimization
3. Ignoring error handling
4. Writing unclear code
5. Not following standards
6. Tight coupling
7. Code duplication
8. Complex solutions

- [SOLID Principles](solid.md)
- Design Patterns
- Testing
- Error Handling 