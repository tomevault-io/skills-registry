---
name: pytest
description: | Use when this capability is needed.
metadata:
  author: jamaliumair
---

# Pytest Testing Skill

Comprehensive testing patterns for Python applications using pytest.

## Quick Reference

| Feature | Reference File |
|---------|----------------|
| Fixtures, conftest, scopes | [references/fixtures.md](references/fixtures.md) |
| Async testing, pytest-asyncio | [references/async-testing.md](references/async-testing.md) |
| Mocking, patching, spies | [references/mocking.md](references/mocking.md) |
| FastAPI/Flask endpoint testing | [references/api-testing.md](references/api-testing.md) |

## Dependencies

```toml
[project.optional-dependencies]
dev = [
    "pytest>=8.0.0",
    "pytest-asyncio>=0.24.0",
    "pytest-cov>=4.1.0",
    "httpx>=0.28.0",           # Async HTTP client for API tests
    "factory-boy>=3.3.0",      # Test factories
    "faker>=33.0.0",           # Fake data generation
]
```

## Configuration

### pyproject.toml

```toml
[tool.pytest.ini_options]
asyncio_mode = "auto"
testpaths = ["tests"]
python_files = ["test_*.py"]
python_functions = ["test_*"]
addopts = "-v --tb=short"
filterwarnings = ["ignore::DeprecationWarning"]

[tool.coverage.run]
source = ["app"]
omit = ["*/tests/*", "*/__init__.py"]
```

## Basic Test Structure

```python
import pytest

# Simple test function
def test_addition():
    assert 1 + 1 == 2

# Test class (group related tests)
class TestCalculator:
    def test_add(self):
        assert add(2, 3) == 5

    def test_subtract(self):
        assert subtract(5, 3) == 2

# Expected exceptions
def test_division_by_zero():
    with pytest.raises(ZeroDivisionError):
        divide(1, 0)

# Parametrized tests
@pytest.mark.parametrize("input,expected", [
    (1, 1),
    (2, 4),
    (3, 9),
])
def test_square(input, expected):
    assert square(input) == expected
```

## Fixtures

```python
import pytest

@pytest.fixture
def sample_user():
    return {"name": "John", "email": "john@example.com"}

@pytest.fixture
def db_connection():
    conn = create_connection()
    yield conn       # Test runs here
    conn.close()     # Cleanup after test

# Use fixture in test
def test_user_name(sample_user):
    assert sample_user["name"] == "John"
```

## Async Testing

```python
import pytest

@pytest.mark.asyncio
async def test_async_function():
    result = await async_operation()
    assert result == "success"

@pytest.fixture
async def async_client():
    async with AsyncClient() as client:
        yield client
```

## Mocking

```python
from unittest.mock import patch, MagicMock, AsyncMock

def test_with_mock():
    with patch("module.external_api") as mock_api:
        mock_api.return_value = {"data": "mocked"}
        result = function_using_api()
        assert result["data"] == "mocked"
        mock_api.assert_called_once()

# Async mock
@pytest.mark.asyncio
async def test_async_mock():
    with patch("module.async_call", new_callable=AsyncMock) as mock:
        mock.return_value = "result"
        result = await function_with_async_call()
        assert result == "result"
```

## Running Tests

```bash
# Run all tests
pytest

# Verbose output
pytest -v

# Run specific file
pytest tests/test_users.py

# Run specific test
pytest tests/test_users.py::test_create_user

# Run with coverage
pytest --cov=app --cov-report=term-missing

# Stop on first failure
pytest -x

# Run last failed tests
pytest --lf

# Run tests matching pattern
pytest -k "user and not delete"
```

## Project Structure

```
project/
├── app/
│   ├── __init__.py
│   ├── main.py
│   └── services/
├── tests/
│   ├── __init__.py
│   ├── conftest.py      # Shared fixtures
│   ├── test_main.py
│   └── test_services/
└── pyproject.toml
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jamaliumair) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
