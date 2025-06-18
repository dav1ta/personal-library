# Python Context Managers

This guide covers Python context managers, their implementation, and common use cases.

## Table of Contents
- [Introduction](#introduction)
- [Basic Usage](#basic-usage)
- [Custom Context Managers](#custom-context-managers)
- [Common Patterns](#common-patterns)
- [Best Practices](#best-practices)

## Introduction

### What are Context Managers?
Context managers are objects that implement the context manager protocol, providing a way to manage resources and handle setup and cleanup operations automatically.

### Key Concepts
- Resource management
- Automatic cleanup
- Exception handling
- Context protocol

## Basic Usage

### File Operations
```python
# Reading a file
with open('data.txt', 'r') as file:
    content = file.read()

# Writing to a file
with open('output.txt', 'w') as file:
    file.write('Hello, World!')
```

### Reading Files in Chunks
```python
with open('large_file.txt', 'r') as file:
    while (chunk := file.read(10000)):
        process_chunk(chunk)
```

### Database Connections
```python
import sqlite3

with sqlite3.connect('database.db') as conn:
    cursor = conn.cursor()
    cursor.execute('SELECT * FROM users')
    results = cursor.fetchall()
```

## Custom Context Managers

### Class-based Implementation
```python
class ListTransaction:
    def __init__(self, thelist):
        self.thelist = thelist
        self.workingcopy = None

    def __enter__(self):
        self.workingcopy = list(self.thelist)
        return self.workingcopy

    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type is None:
            self.thelist[:] = self.workingcopy
        return False
```

### Function-based Implementation
```python
from contextlib import contextmanager

@contextmanager
def managed_resource():
    print("Setting up resource")
    try:
        yield "resource"
    finally:
        print("Cleaning up resource")
```

### Error Handling
```python
class ErrorHandler:
    def __init__(self, error_types):
        self.error_types = error_types

    def __enter__(self):
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type in self.error_types:
            print(f"Handled {exc_type.__name__}")
            return True
        return False
```

## Common Patterns

### Resource Management
```python
class ResourceManager:
    def __init__(self, resource):
        self.resource = resource

    def __enter__(self):
        self.resource.acquire()
        return self.resource

    def __exit__(self, exc_type, exc_val, exc_tb):
        self.resource.release()
```

### Timing Operations
```python
import time

class Timer:
    def __enter__(self):
        self.start = time.time()
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        self.end = time.time()
        self.duration = self.end - self.start
```

### Directory Management
```python
import os
import shutil

class TemporaryDirectory:
    def __init__(self, dirname):
        self.dirname = dirname

    def __enter__(self):
        os.makedirs(self.dirname, exist_ok=True)
        return self.dirname

    def __exit__(self, exc_type, exc_val, exc_tb):
        shutil.rmtree(self.dirname)
```

## Best Practices

1. **Resource Management**
   - Always clean up resources
   - Handle exceptions properly
   - Use appropriate context managers

2. **Error Handling**
   - Implement proper error handling
   - Return appropriate values from __exit__
   - Document error behavior

3. **Performance**
   - Minimize setup/teardown overhead
   - Use appropriate resource management
   - Consider context manager scope

4. **Code Organization**
   - Keep context managers focused
   - Use clear and descriptive names
   - Document behavior clearly

- [Decorators](../advanced/decorators.md)
- Error Handling
- Resource Management
- Performance Optimization 