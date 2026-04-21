---
name: pytest
description: Comprehensive pytest testing framework assistance including test creation, fixture management, parameterized testing, configuration, and advanced testing patterns. Use when Claude needs to work with pytest for: (1) Creating and organizing test files, (2) Managing test fixtures and conftest.py, (3) Implementing parameterized tests, (4) Using pytest marks and decorators, (5) Configuring test environments, or any other pytest testing operations. Use when this capability is needed.
metadata:
  author: malikabk
---

# Pytest Testing Framework Assistant

## Overview

Pytest is a mature full-featured Python testing framework that helps you write better programs. It makes it easy to write small, readable tests that scale to complex functional testing. Pytest provides detailed info on failing assert statements without remembering self.assert* names, auto-discovers test modules and functions, and has a rich plugin architecture with 1000+ external plugins.

## Core Capabilities

### 1. Test Discovery and Execution
- Auto-discovery of test modules and functions
- Flexible test naming patterns (test_*.py or *_test.py)
- Detailed assertion introspection using plain assert statements
- Rich reporting and output customization

### 2. Fixture Management
- Modular fixtures for managing test resources
- Scope-based fixture execution (function, class, module, session)
- Parameterized fixtures and fixture dependencies
- Built-in fixtures like tmp_path, tmpdir, capsys

### 3. Parameterized Testing
- @pytest.mark.parametrize decorator for multiple test inputs
- Stacked parametrization for complex combinations
- Dynamic parametrization with pytest_generate_tests hook

### 4. Test Organization and Control
- Mark decorators for skip, xfail, and custom markers
- Test selection and filtering capabilities
- Configuration via pytest.ini, pyproject.toml, or setup.cfg

## Basic Test Structure

### Simple Test Function
```python
def func(x):
    return x + 1

def test_answer():
    assert func(3) == 5  # Will show detailed failure info
```

### Grouped Tests in Classes
```python
class TestClass:
    def test_one(self):
        x = "this"
        assert "h" in x

    def test_two(self):
        x = "hello"
        assert "world" not in x
```

## Fixture Patterns

### Basic Fixture Definition
```python
import pytest

@pytest.fixture
def my_data():
    return {"key": "value", "list": [1, 2, 3]}

def test_using_fixture(my_data):
    assert my_data["key"] == "value"
    assert len(my_data["list"]) == 3
```

### Scoped Fixtures
```python
@pytest.fixture(scope="session")
def database_connection():
    # Setup code that runs once per test session
    conn = create_connection()
    yield conn
    # Teardown code
    conn.close()

@pytest.fixture(scope="function")
def clean_database(database_connection):
    # Setup for each test function
    clear_database(database_connection)
    yield database_connection
    # Teardown after each test function
    rollback_transactions(database_connection)
```

### Parametrized Fixtures
```python
@pytest.fixture(params=[1, 2, 3])
def number_fixture(request):
    return request.param

def test_numbers(number_fixture):
    assert number_fixture > 0  # Runs 3 times with values 1, 2, 3
```

### Fixture Dependencies
```python
@pytest.fixture
def user_data():
    return {"name": "John", "age": 30}

@pytest.fixture
def user_object(user_data):
    return User(**user_data)

def test_user_methods(user_object):
    assert user_object.name == "John"
    assert user_object.age == 30
```

## Parameterized Testing

### Basic Parametrization
```python
import pytest

@pytest.mark.parametrize("test_input,expected", [
    ("3+5", 8),
    ("2+4", 6),
    ("6*9", 54)  # Note: not 42!
])
def test_eval(test_input, expected):
    assert eval(test_input) == expected
```

### Stacked Parametrization
```python
@pytest.mark.parametrize("x", [0, 1])
@pytest.mark.parametrize("y", [2, 3])
def test_foo(x, y):
    # Creates 4 test cases: x=0,y=2; x=1,y=2; x=0,y=3; x=1,y=3
    pass
```

### Parametrization with Marks
```python
import pytest

@pytest.mark.parametrize("test_input,expected", [
    ("3+5", 8),
    pytest.param("6*9", 42, marks=pytest.mark.xfail),  # Expected to fail
    ("2*5", 10)
])
def test_eval(test_input, expected):
    assert eval(test_input) == expected
```

## Mark Patterns

### Skip and XFail
```python
import pytest
import sys

@pytest.mark.skip(reason="Not implemented yet")
def test_skip_example():
    pass

@pytest.mark.skipif(sys.platform == "win32", reason="Does not run on Windows")
def test_not_on_windows():
    pass

@pytest.mark.xfail(condition=True, reason="Known bug")
def test_expected_failure():
    assert False
```

### Custom Marks
```python
import pytest

@pytest.mark.slow
@pytest.mark.integration
def test_integration():
    # Long-running integration test
    pass

@pytest.mark.timeout(10, method="thread")
def test_timeout():
    # Test that should not exceed 10 seconds
    pass
```

## Exception Testing

### Testing for Expected Exceptions
```python
import pytest

def divide(a, b):
    if b == 0:
        raise ZeroDivisionError("Cannot divide by zero")
    return a / b

def test_divide_by_zero():
    with pytest.raises(ZeroDivisionError):
        divide(10, 0)

def test_exception_message():
    with pytest.raises(ZeroDivisionError, match="Cannot divide by zero"):
        divide(10, 0)
```

## Floating Point Comparisons

### Handling Precision Issues
```python
def test_float_comparison():
    # Use pytest.approx() for floating-point comparisons
    assert (0.1 + 0.2) == pytest.approx(0.3)

def test_approx_with_tolerance():
    # Custom tolerance
    assert 1.0001 == pytest.approx(1.0, abs=1e-3)
```

## Built-in Fixtures

### Common Built-in Fixtures
```python
def test_temp_directory(tmp_path):
    """Use tmp_path for temporary directory testing."""
    file_path = tmp_path / "test_file.txt"
    file_path.write_text("content")
    assert file_path.read_text() == "content"

def test_capture_stdout(capsys):
    """Capture stdout and stderr."""
    print("hello")
    captured = capsys.readouterr()
    assert captured.out == "hello\n"

def test_capture_logs(caplog):
    """Capture log output."""
    import logging
    logger = logging.getLogger(__name__)
    logger.warning("test message")
    assert "test message" in caplog.text
```

## Configuration Files

### pytest.ini Example
```ini
[tool:pytest]
minversion = 6.0
testpaths = tests
python_files = test_*.py *_test.py
python_classes = Test*
python_functions = test_*
addopts =
    -ra
    --strict-markers
    --strict-config
markers =
    slow: marks tests as slow
    fast: marks tests as fast
    integration: marks tests as integration tests
xfail_strict = true
```

### pyproject.toml Example
```toml
[tool.pytest.ini_options]
minversion = "6.0"
testpaths = ["tests"]
python_files = ["test_*.py", "*_test.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
addopts = [
    "-ra",
    "--strict-markers",
    "--strict-config",
]
markers = [
    "slow: marks tests as slow",
    "fast: marks tests as fast",
    "integration: marks tests as integration tests",
]
xfail_strict = true
```

## conftest.py Patterns

### Basic conftest.py Structure
```python
# conftest.py
import pytest

@pytest.fixture
def sample_data():
    """Shared fixture available to all tests in this directory and subdirectories."""
    return {"name": "test", "value": 42}

@pytest.fixture(scope="session")
def database():
    """Session-scoped fixture for database connection."""
    # Setup
    db = create_test_database()
    yield db
    # Teardown
    destroy_test_database(db)
```

### Multiple conftest.py Files
```
project/
├── conftest.py              # Global fixtures
├── unit/
│   └── conftest.py          # Unit test specific fixtures
└── integration/
    └── conftest.py          # Integration test specific fixtures
```

## Advanced Patterns

### Custom Hooks in conftest.py
```python
# conftest.py
def pytest_configure(config):
    """Called after command line options have been parsed."""
    config.addinivalue_line("markers", "slow: mark test as slow")

def pytest_runtest_protocol(item, nextitem):
    """Called to execute the test protocol for a single test item."""
    # Custom test execution logic
    pass

def pytest_generate_tests(metafunc):
    """Called when collecting a test function to generate parametrized tests."""
    if "username" in metafunc.fixturenames:
        metafunc.parametrize("username", ["user1", "user2", "user3"])
```

### Conditional Fixture Parameters
```python
import pytest

def pytest_configure(config):
    config.addinivalue_line(
        "markers", "db: mark test as database test"
    )

@pytest.fixture
def db_connection(request):
    if request.node.get_closest_marker("db"):
        # Use real database for marked tests
        return RealDatabaseConnection()
    else:
        # Use mock for unmarked tests
        return MockDatabaseConnection()
```

## Command Line Usage

### Common Command Patterns
```bash
# Run all tests
pytest

# Run specific test file
pytest tests/test_module.py

# Run specific test function
pytest tests/test_module.py::test_function

# Run tests matching pattern
pytest -k "test_name_contains"

# Run tests with specific marker
pytest -m "slow"

# Run tests excluding specific marker
pytest -m "not slow"

# Verbose output
pytest -v

# Stop after first failure
pytest -x

# Stop after N failures
pytest --maxfail=2

# Show local variables in tracebacks
pytest -l

# Run tests in parallel (requires pytest-xdist)
pytest -n auto
```

## Integration with Other Tools

### Coverage Integration
```bash
# Run tests with coverage
pytest --cov=myproject

# Generate HTML coverage report
pytest --cov=myproject --cov-report=html
```

### Type Checking Integration
```bash
# Run tests with mypy
pytest --mypy

# Run tests with type checking
pytest --typecov
```

## Resources

This skill includes resources for different aspects of pytest development:

### scripts/
Python and shell scripts for common pytest operations.

**Examples:**
- `generate_test_scaffold.py` - Script to generate test structure with fixtures
- `run_parametrized_tests.py` - Script to run parameterized test configurations
- `create_conftest.py` - Script to generate conftest.py with common fixtures

### references/
Detailed documentation and reference materials for pytest features.

**Examples:**
- `fixture_patterns.md` - Advanced fixture patterns and best practices
- `parametrization_guide.md` - Comprehensive parameterization strategies
- `configuration_options.md` - Complete pytest configuration reference
- `plugin_integrations.md` - Popular pytest plugins and usage patterns

### assets/
Project templates and boilerplate code for common pytest setups.

**Examples:**
- `templates/basic-test-suite/` - Basic pytest project template
- `templates/parametrized-tests/` - Template with parameterized testing patterns
- `templates/fixture-patterns/` - Template with advanced fixture organization

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/malikabk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
