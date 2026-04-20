---
name: pytest
description: Comprehensive pytest patterns for Python testing - fixtures, mocking, parametrization, markers, and TDD workflow Use when this capability is needed.
metadata:
  author: businessplus-community
---

# Pytest Testing Patterns

Comprehensive testing strategies using pytest for Python applications.

## When to Activate

- Writing new tests for Python code
- Setting up test infrastructure
- Debugging test failures
- Learning pytest patterns

## Core Philosophy: TDD

Always follow Red-Green-Refactor:

1. **RED**: Write a failing test that describes desired behavior
2. **GREEN**: Write minimal code to make the test pass
3. **REFACTOR**: Improve code while keeping tests green

## Basic Test Structure

```python
import pytest

def test_addition():
    """Test basic addition."""
    assert 2 + 2 == 4

def test_string_uppercase():
    """Test string uppercasing."""
    text = "hello"
    assert text.upper() == "HELLO"
```

### Assertions

```python
# Equality
assert result == expected
assert result != unexpected

# Truthiness
assert result              # Truthy
assert not result          # Falsy
assert result is True      # Exactly True
assert result is None      # Exactly None

# Membership
assert item in collection
assert item not in collection

# Type checking
assert isinstance(result, str)

# Exception testing
with pytest.raises(ValueError):
    raise ValueError("error")

# Check exception message
with pytest.raises(ValueError, match="invalid input"):
    validate("bad data")

# Access exception attributes
with pytest.raises(ValueError) as exc_info:
    raise ValueError("error")
assert str(exc_info.value) == "error"
```

## Fixtures

### Basic Fixture

```python
@pytest.fixture
def sample_data():
    """Fixture providing sample data."""
    return {"name": "Alice", "age": 30}

def test_sample_data(sample_data):
    """Test using the fixture."""
    assert sample_data["name"] == "Alice"
```

### Fixture with Setup/Teardown

```python
@pytest.fixture
def database():
    """Fixture with setup and teardown."""
    # Setup
    db = Database(":memory:")
    db.create_tables()

    yield db  # Provide to test

    # Teardown
    db.close()

def test_database_query(database):
    result = database.query("SELECT 1")
    assert result is not None
```

### Fixture Scopes

```python
# Function scope (default) - runs for each test
@pytest.fixture
def fresh_data():
    return {"count": 0}

# Module scope - runs once per test module
@pytest.fixture(scope="module")
def module_db():
    db = Database(":memory:")
    yield db
    db.close()

# Session scope - runs once per test session
@pytest.fixture(scope="session")
def expensive_resource():
    resource = ExpensiveSetup()
    yield resource
    resource.cleanup()
```

### Autouse Fixtures

```python
@pytest.fixture(autouse=True)
def reset_config():
    """Automatically runs before every test."""
    Config.reset()
    yield
    Config.cleanup()
```

### conftest.py for Shared Fixtures

```python
# tests/conftest.py - available to all tests in directory
import pytest

@pytest.fixture
def api_client():
    """Shared fixture for API testing."""
    from myapp import create_app
    app = create_app(testing=True)
    with app.test_client() as client:
        yield client
```

## Parametrization

### Basic Parametrization

```python
@pytest.mark.parametrize("input,expected", [
    ("hello", "HELLO"),
    ("world", "WORLD"),
    ("PyThOn", "PYTHON"),
])
def test_uppercase(input, expected):
    """Test runs 3 times with different inputs."""
    assert input.upper() == expected
```

### Multiple Parameters

```python
@pytest.mark.parametrize("a,b,expected", [
    (2, 3, 5),
    (0, 0, 0),
    (-1, 1, 0),
    (100, 200, 300),
])
def test_add(a, b, expected):
    assert add(a, b) == expected
```

### Parametrize with IDs

```python
@pytest.mark.parametrize("input,expected", [
    ("valid@email.com", True),
    ("invalid", False),
    ("@no-domain.com", False),
], ids=["valid-email", "missing-at", "missing-domain"])
def test_email_validation(input, expected):
    assert is_valid_email(input) is expected
```

## Markers

### Custom Markers

```python
# Mark slow tests
@pytest.mark.slow
def test_slow_operation():
    time.sleep(5)

# Mark integration tests
@pytest.mark.integration
def test_api_integration():
    response = requests.get("https://api.example.com")
    assert response.status_code == 200

# Mark unit tests
@pytest.mark.unit
def test_unit_logic():
    assert calculate(2, 3) == 5
```

### Configure in pyproject.toml

```toml
[tool.pytest.ini_options]
markers = [
    "slow: marks tests as slow",
    "integration: marks tests as integration tests",
    "unit: marks tests as unit tests",
]
```

### Run by Marker

```bash
# Run only fast tests
uv run pytest -m "not slow"

# Run only integration tests
uv run pytest -m integration

# Combine markers
uv run pytest -m "unit and not slow"
```

## Mocking

### Mock Functions

```python
from unittest.mock import patch, Mock

@patch("mypackage.external_api_call")
def test_with_mock(api_call_mock):
    """Test with mocked external API."""
    api_call_mock.return_value = {"status": "success"}

    result = my_function()

    api_call_mock.assert_called_once()
    assert result["status"] == "success"
```

### Mock Return Values

```python
@patch("mypackage.Database.connect")
def test_database_connection(connect_mock):
    connect_mock.return_value = MockConnection()

    db = Database()
    db.connect()

    connect_mock.assert_called_once()
```

### Mock Exceptions

```python
@patch("mypackage.api_call")
def test_api_error_handling(api_call_mock):
    api_call_mock.side_effect = ConnectionError("Network error")

    with pytest.raises(ConnectionError):
        api_call()
```

### Mock File Operations

```python
from unittest.mock import mock_open

@patch("builtins.open", mock_open(read_data="file content"))
def test_file_reading(mock_file):
    result = read_file("test.txt")
    assert result == "file content"
```

## Testing Async Code

```python
import pytest

@pytest.mark.asyncio
async def test_async_function():
    result = await async_add(2, 3)
    assert result == 5
```

## Built-in Fixtures

### tmp_path - Temporary Directory

```python
def test_with_tmp_path(tmp_path):
    """pytest provides tmp_path fixture."""
    test_file = tmp_path / "test.txt"
    test_file.write_text("hello world")

    result = process_file(str(test_file))
    assert result == "hello world"
    # tmp_path automatically cleaned up
```

### capsys - Capture stdout/stderr

```python
def test_print_output(capsys):
    print("Hello, World!")

    captured = capsys.readouterr()
    assert captured.out == "Hello, World!\n"
```

### monkeypatch - Modify Objects

```python
def test_with_monkeypatch(monkeypatch):
    monkeypatch.setenv("API_KEY", "test-key")
    monkeypatch.setattr("mymodule.CONFIG", {"debug": True})

    # Test code that uses these values
```

## Test Organization

### Directory Structure

```
tests/
├── conftest.py           # Shared fixtures
├── __init__.py
├── unit/                 # Fast, isolated tests
│   ├── test_models.py
│   └── test_utils.py
└── integration/          # Tests with real dependencies
    └── test_api.py
```

### Test Classes

```python
class TestUserService:
    """Group related tests."""

    @pytest.fixture(autouse=True)
    def setup(self):
        self.service = UserService()

    def test_create_user(self):
        user = self.service.create_user("Alice")
        assert user.name == "Alice"

    def test_delete_user(self):
        user = User(id=1, name="Bob")
        self.service.delete_user(user)
        assert not self.service.user_exists(1)
```

## Running Tests

```bash
# Run all tests
uv run pytest

# Run specific file
uv run pytest tests/test_utils.py

# Run specific test
uv run pytest tests/test_utils.py::test_function

# Run with verbose output
uv run pytest -v

# Run with coverage
uv run pytest --cov=src --cov-report=term-missing

# Run until first failure
uv run pytest -x

# Run last failed tests
uv run pytest --lf

# Run tests matching pattern
uv run pytest -k "test_user"

# Run with debugger on failure
uv run pytest --pdb
```

## pyproject.toml Configuration

```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_functions = ["test_*"]
addopts = [
    "-q",
    "--strict-markers",
    "--cov=src",
    "--cov-report=term-missing",
]
markers = [
    "slow: marks tests as slow",
    "integration: marks tests as integration tests",
    "unit: marks tests as unit tests",
]
```

## Quick Reference

| Pattern | Usage |
|---------|-------|
| `pytest.raises()` | Test expected exceptions |
| `@pytest.fixture` | Create reusable test setup |
| `@pytest.mark.parametrize` | Run tests with multiple inputs |
| `@pytest.mark.slow` | Mark slow tests |
| `@patch()` | Mock functions/classes |
| `tmp_path` fixture | Automatic temp directory |
| `-m "not slow"` | Skip slow tests |
| `--cov` | Generate coverage report |

## Best Practices

### DO

- Follow TDD: Write tests before code
- Test one thing per test
- Use descriptive names: `test_user_login_with_invalid_credentials_fails`
- Use fixtures for reusable setup
- Mock external dependencies
- Aim for 80%+ coverage

### DON'T

- Test implementation details
- Share state between tests
- Catch exceptions in tests (use `pytest.raises`)
- Test third-party library code
- Ignore test failures

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/businessplus-community) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
