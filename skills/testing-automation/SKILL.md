---
name: Testing Automation
description: Generate and execute automated tests for code quality assurance
version: 1.0.0
author: LLM Tools Project
tags: [testing, automation, quality-assurance, pytest, unittest]
---

# Testing Automation Skill

This skill provides guidance for creating automated tests, implementing test-driven development (TDD), and ensuring code quality through comprehensive testing strategies.

## Overview

Testing automation involves writing unit tests, integration tests, and end-to-end tests to verify code correctness, prevent regressions, and maintain code quality. This skill covers test generation, execution, and best practices.

## When to Use This Skill

Use this skill when:
- ✅ You need to verify code correctness
- ✅ You're implementing new features
- ✅ You want to prevent regressions
- ✅ You're refactoring existing code
- ✅ You need to document expected behavior

Don't use this skill when:
- ❌ Writing throwaway prototype code
- ❌ Time constraints prevent proper testing (reconsider this!)

## Prerequisites

**Python Testing**:
```bash
pip install pytest pytest-cov pytest-mock
```

**JavaScript Testing**:
```bash
npm install --save-dev jest @testing-library/react
```

## Instructions

### Step 1: Write Unit Tests

```python
import pytest
from myapp.calculator import Calculator

class TestCalculator:
    """Test suite for Calculator class."""
    
    def setup_method(self):
        """Setup run before each test method."""
        self.calc = Calculator()
    
    def test_addition(self):
        """Test addition operation."""
        result = self.calc.add(2, 3)
        assert result == 5
    
    def test_division(self):
        """Test division operation."""
        result = self.calc.divide(10, 2)
        assert result == 5.0
    
    def test_division_by_zero(self):
        """Test division by zero raises error."""
        with pytest.raises(ValueError, match="Cannot divide by zero"):
            self.calc.divide(10, 0)
    
    @pytest.mark.parametrize("a,b,expected", [
        (1, 1, 2),
        (0, 0, 0),
        (-1, 1, 0),
        (100, 200, 300),
    ])
    def test_addition_parametrized(self, a, b, expected):
        """Test addition with multiple inputs."""
        assert self.calc.add(a, b) == expected
```

### Step 2: Mock External Dependencies

```python
import pytest
from unittest.mock import Mock, patch
from myapp.user_service import UserService

class TestUserService:
    """Test UserService with mocked database."""
    
    @patch('myapp.user_service.Database')
    def test_get_user(self, mock_db):
        """Test getting user from database."""
        # Setup mock
        mock_db.return_value.query.return_value = {
            'id': 1,
            'name': 'John Doe'
        }
        
        # Test
        service = UserService()
        user = service.get_user(1)
        
        # Verify
        assert user['name'] == 'John Doe'
        mock_db.return_value.query.assert_called_once_with('users', id=1)
    
    def test_create_user_with_mock(self):
        """Test user creation with mock database."""
        mock_db = Mock()
        service = UserService(database=mock_db)
        
        user_data = {'name': 'Jane Doe', 'email': 'jane@example.com'}
        service.create_user(user_data)
        
        mock_db.insert.assert_called_once_with('users', user_data)
```

### Step 3: Test Coverage

```python
# Run tests with coverage
# pytest --cov=myapp --cov-report=html tests/

# In your test configuration (pytest.ini or pyproject.toml)
# [tool.pytest.ini_options]
# testpaths = ["tests"]
# python_files = ["test_*.py"]
# python_classes = ["Test*"]
# python_functions = ["test_*"]
# addopts = "--cov=myapp --cov-report=term-missing --cov-fail-under=80"
```

### Step 4: Integration Tests

```python
import pytest
from myapp import create_app
from myapp.database import db

@pytest.fixture
def app():
    """Create application for testing."""
    app = create_app('testing')
    
    with app.app_context():
        db.create_all()
        yield app
        db.drop_all()

@pytest.fixture
def client(app):
    """Create test client."""
    return app.test_client()

class TestUserAPI:
    """Integration tests for User API."""
    
    def test_create_user(self, client):
        """Test user creation endpoint."""
        response = client.post('/api/users', json={
            'name': 'Test User',
            'email': 'test@example.com'
        })
        
        assert response.status_code == 201
        data = response.get_json()
        assert data['name'] == 'Test User'
        assert 'id' in data
    
    def test_get_user(self, client):
        """Test getting user endpoint."""
        # Create user first
        create_response = client.post('/api/users', json={
            'name': 'Test User',
            'email': 'test@example.com'
        })
        user_id = create_response.get_json()['id']
        
        # Get user
        response = client.get(f'/api/users/{user_id}')
        assert response.status_code == 200
        assert response.get_json()['name'] == 'Test User'
```

## Examples

### Example 1: TDD Workflow

**Scenario**: Implement a function using Test-Driven Development

```python
# Step 1: Write the test first (it will fail)
def test_calculate_discount():
    """Test discount calculation."""
    assert calculate_discount(100, 10) == 90
    assert calculate_discount(50, 20) == 40
    assert calculate_discount(100, 0) == 100

# Step 2: Implement minimal code to pass
def calculate_discount(price, discount_percent):
    """Calculate price after discount."""
    return price - (price * discount_percent / 100)

# Step 3: Run tests - they should pass
# Step 4: Refactor if needed while keeping tests green
```

### Example 2: Testing Async Code

```python
import pytest
import asyncio

@pytest.mark.asyncio
async def test_async_function():
    """Test asynchronous function."""
    result = await fetch_data_async('https://api.example.com')
    assert result is not None
    assert 'data' in result

@pytest.mark.asyncio
async def test_concurrent_requests():
    """Test multiple concurrent requests."""
    urls = ['url1', 'url2', 'url3']
    results = await asyncio.gather(*[fetch_data_async(url) for url in urls])
    assert len(results) == 3
    assert all(r is not None for r in results)
```

## Best Practices

### Do's
- ✅ **Write tests first** (TDD approach)
- ✅ **Test one thing per test** (focused tests)
- ✅ **Use descriptive test names** (document behavior)
- ✅ **Test edge cases** (empty inputs, nulls, boundaries)
- ✅ **Keep tests independent** (no shared state)
- ✅ **Use fixtures** for setup/teardown
- ✅ **Aim for high coverage** (80%+ is good)
- ✅ **Test error conditions** (not just happy path)

### Don'ts
- ❌ **Don't test implementation details** (test behavior)
- ❌ **Don't write flaky tests** (inconsistent results)
- ❌ **Don't skip edge cases**
- ❌ **Don't ignore failing tests**
- ❌ **Don't make tests dependent** on each other
- ❌ **Don't test external services** without mocking
- ❌ **Don't write slow tests** (optimize or mark as slow)

## Common Pitfalls

### Pitfall 1: Testing Implementation Instead of Behavior

**Problem**: Tests break when refactoring

**Solution**: Test public interface, not internal details

```python
# Bad: Testing implementation
def test_user_storage():
    user = User()
    assert user._internal_dict == {}  # Don't test private details

# Good: Testing behavior
def test_user_creation():
    user = User(name="John")
    assert user.get_name() == "John"  # Test public interface
```

### Pitfall 2: Shared State Between Tests

**Problem**: Tests pass individually but fail when run together

**Solution**: Use fixtures and ensure test isolation

```python
# Bad: Shared state
users = []

def test_add_user():
    users.append("John")
    assert len(users) == 1

def test_another():
    assert len(users) == 0  # Fails if test_add_user runs first

# Good: Isolated state
@pytest.fixture
def users():
    return []

def test_add_user(users):
    users.append("John")
    assert len(users) == 1
```

### Pitfall 3: Not Mocking External Dependencies

**Problem**: Tests fail due to network issues or external service downtime

**Solution**: Mock external dependencies

```python
# Bad: Real API call
def test_fetch_user():
    user = fetch_from_api('https://api.example.com/users/1')
    assert user['name'] == 'John'

# Good: Mocked API call
@patch('myapp.api.requests.get')
def test_fetch_user(mock_get):
    mock_get.return_value.json.return_value = {'name': 'John'}
    user = fetch_from_api('https://api.example.com/users/1')
    assert user['name'] == 'John'
```

## Test Organization

```
project/
├── src/
│   └── myapp/
│       ├── __init__.py
│       ├── models.py
│       └── services.py
└── tests/
    ├── __init__.py
    ├── conftest.py          # Shared fixtures
    ├── unit/
    │   ├── test_models.py
    │   └── test_services.py
    ├── integration/
    │   └── test_api.py
    └── e2e/
        └── test_workflows.py
```

## Performance Considerations

- **Mark slow tests**: Use `@pytest.mark.slow` for long-running tests
- **Run fast tests first**: Quick feedback loop
- **Parallelize tests**: Use `pytest-xdist` for parallel execution
- **Use test databases**: In-memory SQLite for faster tests
- **Mock expensive operations**: Don't hit real databases/APIs

```bash
# Run tests in parallel
pytest -n auto

# Run only fast tests
pytest -m "not slow"
```

## Related Skills

- **debugging** - Use when tests fail
- **code-review** - Ensure test quality
- **api-integration** - Test API integrations

## Additional Resources

- [Pytest Documentation](https://docs.pytest.org/)
- [Testing Best Practices](https://testdriven.io/blog/testing-best-practices/)
- [Test-Driven Development](https://www.obeythetestinggoat.com/)

## Version History

- **1.0.0** (2026-02-16): Initial version
