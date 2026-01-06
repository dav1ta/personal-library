# Advanced Testing in Python

This guide covers advanced testing techniques for Python applications, focusing on challenging scenarios and best practices.

## Table of Contents
- [Introduction](#introduction)
- [Test Organization](#test-organization)
- [Fixtures and Setup](#fixtures-and-setup)
- [Advanced Testing Scenarios](#advanced-testing-scenarios)
- [Mocking and Stubbing](#mocking-and-stubbing)
- [Performance Testing](#performance-testing)
- [Best Practices](#best-practices)

## Introduction

### Testing Challenges
- Concurrency and parallelism
- Time-dependent behavior
- Randomness and stochastic processes
- Error handling and edge cases
- External dependencies
- File system interactions
- Network conditions
- Configuration variations

### Testing Tools
- pytest: Modern testing framework
- unittest: Standard library testing
- mock: Mocking library
- freezegun: Time mocking
- requests-mock: HTTP mocking
- pytest-asyncio: Async testing

## Test Organization

### Test Structure
```python
# test_example.py
import pytest

def test_basic_functionality():
    assert True

class TestClass:
    def test_method(self):
        assert True

    @pytest.mark.parametrize("input,expected", [
        (1, 2),
        (2, 4),
        (3, 6)
    ])
    def test_parameterized(self, input, expected):
        assert input * 2 == expected
```

### Test Categories
```python
@pytest.mark.unit
def test_unit():
    pass

@pytest.mark.integration
def test_integration():
    pass

@pytest.mark.e2e
def test_e2e():
    pass
```

## Fixtures and Setup

### Basic Fixtures
```python
@pytest.fixture
def sample_data():
    return {"key": "value"}

def test_with_fixture(sample_data):
    assert sample_data["key"] == "value"
```

### Fixture Scope
```python
@pytest.fixture(scope="session")
def db_connection():
    # Setup
    connection = create_connection()
    yield connection
    # Teardown
    connection.close()

@pytest.fixture(scope="function")
def test_data():
    return {"id": 1}
```

### Fixture Dependencies
```python
@pytest.fixture
def user(db_connection):
    return create_user(db_connection)

@pytest.fixture
def user_with_profile(user):
    return add_profile(user)
```

## Advanced Testing Scenarios

### Concurrency Testing
```python
import pytest
import threading

@pytest.fixture
def shared_counter():
    return {"value": 0}

def test_thread_safety(shared_counter):
    def increment():
        shared_counter["value"] += 1

    threads = [threading.Thread(target=increment) for _ in range(5)]
    for t in threads:
        t.start()
    for t in threads:
        t.join()

    assert shared_counter["value"] == 5
```

### Async Testing
```python
import pytest
import asyncio

@pytest.mark.asyncio
async def test_async_operation():
    async def operation():
        await asyncio.sleep(0.1)
        return "result"
    
    result = await operation()
    assert result == "result"
```

### Time-Dependent Testing
```python
from freezegun import freeze_time
from datetime import datetime

@freeze_time("2025-01-01 12:00:00")
def test_time_dependent():
    now = datetime.now()
    assert now.year == 2025
    assert now.hour == 12
```

## Mocking and Stubbing

### Basic Mocking
```python
from unittest.mock import Mock, patch

def test_mock_function():
    mock_func = Mock(return_value=42)
    assert mock_func() == 42
    mock_func.assert_called_once()
```

### API Mocking
```python
import requests
import requests_mock

def test_api_call():
    with requests_mock.Mocker() as mock:
        mock.get("https://api.example.com/data", json={"key": "value"})
        response = requests.get("https://api.example.com/data")
        assert response.json() == {"key": "value"}
```

### Database Mocking
```python
from unittest.mock import Mock

def test_database_operation():
    mock_db = Mock()
    mock_db.query.return_value = [{"id": 1, "name": "test"}]
    
    result = mock_db.query("SELECT * FROM users")
    assert result == [{"id": 1, "name": "test"}]
```

## Performance Testing

### Basic Performance Test
```python
import time
import pytest

def test_performance():
    start_time = time.time()
    # Perform operation
    end_time = time.time()
    assert end_time - start_time < 1.0  # Should complete within 1 second
```

### Memory Profiling
```python
import pytest
from memory_profiler import profile

@profile
def test_memory_usage():
    # Perform memory-intensive operation
    pass
```

### Load Testing
```python
import pytest
import asyncio

@pytest.mark.asyncio
async def test_concurrent_requests():
    async def make_request():
        # Simulate request
        await asyncio.sleep(0.1)
        return "response"

    tasks = [make_request() for _ in range(100)]
    responses = await asyncio.gather(*tasks)
    assert len(responses) == 100
```

## Best Practices

1. **Test Organization**
   - Use clear test names
   - Group related tests
   - Follow AAA pattern (Arrange, Act, Assert)

2. **Test Isolation**
   - Each test should be independent
   - Clean up resources properly
   - Use appropriate fixtures

3. **Error Handling**
   - Test both success and failure cases
   - Verify error messages
   - Test edge cases

4. **Maintenance**
   - Keep tests simple and focused
   - Document complex test scenarios
   - Regular test maintenance

- Debugging
- Performance Optimization
- Error Handling
- Concurrency 

Next: [Pytest Fixtures](pytest_fixtures.md)
