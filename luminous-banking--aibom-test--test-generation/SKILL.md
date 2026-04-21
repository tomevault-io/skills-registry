---
name: test-generation
description: Generate unit tests and integration tests for Python code using pytest. Use when the user asks to write tests, needs test coverage, or wants to test specific functions or modules. Use when this capability is needed.
metadata:
  author: luminous-banking
---

# Test Generation

Generate comprehensive tests for Python code using pytest.

## Quick Start

When generating tests:

1. Analyze the code to understand its behavior
2. Identify test cases (happy path, edge cases, errors)
3. Write tests following pytest conventions
4. Ensure tests are independent and repeatable

## Test File Structure

```
tests/
├── __init__.py
├── conftest.py           # Shared fixtures
├── unit/
│   ├── __init__.py
│   └── test_module.py
└── integration/
    ├── __init__.py
    └── test_api.py
```

## Pytest Conventions

### Test Function Naming
```python
# Pattern: test_<function>_<scenario>_<expected_result>
def test_calculate_total_with_discount_returns_reduced_price():
    ...

def test_create_user_with_invalid_email_raises_validation_error():
    ...
```

### Basic Test Structure
```python
import pytest
from mymodule import calculate_total

class TestCalculateTotal:
    """Tests for the calculate_total function."""

    def test_returns_sum_of_items(self):
        """Happy path: normal calculation."""
        items = [10, 20, 30]
        result = calculate_total(items)
        assert result == 60

    def test_empty_list_returns_zero(self):
        """Edge case: empty input."""
        result = calculate_total([])
        assert result == 0

    def test_negative_values_raises_error(self):
        """Error case: invalid input."""
        with pytest.raises(ValueError, match="negative"):
            calculate_total([-1, 10])
```

## Fixtures

### Basic Fixture
```python
@pytest.fixture
def sample_user():
    """Create a sample user for testing."""
    return {
        "id": "user-123",
        "name": "Test User",
        "email": "test@example.com"
    }

def test_user_creation(sample_user):
    user = User(**sample_user)
    assert user.email == "test@example.com"
```

### Fixture with Cleanup
```python
@pytest.fixture
def temp_database():
    """Create and cleanup a temporary database."""
    db = create_test_database()
    yield db
    db.cleanup()
```

### Parameterized Fixtures
```python
@pytest.fixture(params=["sqlite", "postgres"])
def database(request):
    """Test with multiple database backends."""
    return create_database(backend=request.param)
```

## Parameterized Tests

```python
@pytest.mark.parametrize("input_val,expected", [
    (0, "zero"),
    (1, "one"),
    (2, "two"),
    (-1, "negative"),
])
def test_number_to_word(input_val, expected):
    assert number_to_word(input_val) == expected
```

## Mocking

### Mock External Services
```python
from unittest.mock import Mock, patch

def test_fetch_user_data():
    with patch('mymodule.requests.get') as mock_get:
        mock_get.return_value.json.return_value = {"id": "123"}
        mock_get.return_value.status_code = 200

        result = fetch_user_data("123")

        assert result["id"] == "123"
        mock_get.assert_called_once()
```

### Mock Objects
```python
@pytest.fixture
def mock_database():
    db = Mock()
    db.query.return_value = [{"id": 1}, {"id": 2}]
    return db

def test_list_users(mock_database):
    service = UserService(mock_database)
    users = service.list_users()
    assert len(users) == 2
```

## Test Categories

Generate tests for these scenarios:

1. **Happy Path**: Normal expected usage
2. **Edge Cases**: Boundary conditions, empty inputs
3. **Error Cases**: Invalid inputs, exceptions
4. **Integration**: Component interactions

## Running Tests

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=mymodule --cov-report=html

# Run specific test file
pytest tests/unit/test_module.py

# Run tests matching pattern
pytest -k "test_user"

# Verbose output
pytest -v
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luminous-banking) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
