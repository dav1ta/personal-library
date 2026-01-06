# Pytest Fixtures

This guide covers pytest fixtures, their usage, and best practices for test setup and teardown.

## Table of Contents
- [Introduction](#introduction)
- [Basic Fixtures](#basic-fixtures)
- [Fixture Scope](#fixture-scope)
- [Fixture Dependencies](#fixture-dependencies)
- [Built-in Fixtures](#built-in-fixtures)
- [Best Practices](#best-practices)

## Introduction

### What are Fixtures?
Fixtures are functions that provide test data or test resources. They can be used to set up preconditions for tests and clean up after tests.

### Key Concepts
- Test setup and teardown
- Resource management
- Test isolation
- Reusable test components

## Basic Fixtures

### Simple Fixture
```python
import pytest

@pytest.fixture
def sample_data():
    return {"key": "value"}

def test_with_fixture(sample_data):
    assert sample_data["key"] == "value"
```

### Class Instance Fixture
```python
import pytest

class Calculator:
    def add(self, x, y):
        return x + y

    def subtract(self, x, y):
        return x - y

@pytest.fixture
def calculator():
    return Calculator()

def test_addition(calculator):
    assert calculator.add(2, 3) == 5

def test_subtraction(calculator):
    assert calculator.subtract(5, 3) == 2
```

## Fixture Scope

### Function Scope (Default)
```python
@pytest.fixture
def function_scope_fixture():
    print("Setup")
    yield
    print("Teardown")
```

### Class Scope
```python
@pytest.fixture(scope="class")
def class_scope_fixture():
    print("Class setup")
    yield
    print("Class teardown")
```

### Module Scope
```python
@pytest.fixture(scope="module")
def module_scope_fixture():
    print("Module setup")
    yield
    print("Module teardown")
```

### Session Scope
```python
@pytest.fixture(scope="session")
def session_scope_fixture():
    print("Session setup")
    yield
    print("Session teardown")
```

## Fixture Dependencies

### Basic Dependencies
```python
@pytest.fixture
def database():
    return Database()

@pytest.fixture
def user(database):
    return database.create_user()
```

### Multiple Dependencies
```python
@pytest.fixture
def config():
    return {"host": "localhost", "port": 5432}

@pytest.fixture
def database(config):
    return Database(config)

@pytest.fixture
def user(database, config):
    return database.create_user(config)
```

## Built-in Fixtures

### Temporary Directory
```python
def test_file_operations(tmp_path):
    file_path = tmp_path / "test.txt"
    file_path.write_text("Hello, World!")
    assert file_path.read_text() == "Hello, World!"
```

### Temporary File
```python
def test_file_operations(tmp_path):
    with tempfile.NamedTemporaryFile(dir=tmp_path) as f:
        f.write(b"Hello, World!")
        f.flush()
        assert f.read() == b"Hello, World!"
```

### Monkeypatch
```python
def test_monkeypatch(monkeypatch):
    def mock_time():
        return 1234567890
    monkeypatch.setattr(time, "time", mock_time)
    assert time.time() == 1234567890
```

## Best Practices

1. **Fixture Organization**
   - Keep fixtures focused and single-purpose
   - Use clear and descriptive names
   - Document fixture behavior

2. **Resource Management**
   - Use appropriate scopes
   - Clean up resources properly
   - Handle exceptions in fixtures

3. **Test Isolation**
   - Avoid shared state
   - Use appropriate scopes
   - Clean up after tests

4. **Performance**
   - Use appropriate scopes
   - Avoid unnecessary setup
   - Reuse fixtures when possible

- [Testing](../advanced/testing.md)
- Debugging
- Error Handling
- Performance Optimization 

Next: [Test Examples](../testing/test.md)
