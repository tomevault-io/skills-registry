---
name: test-discord-api
description: Write and run unit tests for discord_api following best practices. Use when the user asks to write tests, add test coverage, or run tests for API code. Use when this capability is needed.
metadata:
  author: zmchenry
---

# Test Discord API

This skill helps write and run unit tests for discord_api following the project's conventions and best practices.

## Quick Start

When the user asks to write or run tests:

1. Examine existing test patterns in the same module
2. Write succinct tests covering core logic (80/20 principle)
3. Run tests using api-test-daemon (preferred) or fallback to clyde api test
4. Ensure tests are owned by safety-experience

## Test Writing Principles

### Core Principles
- **Succinct but comprehensive**: Cover core logic with minimal code (80/20 rule)
- **No test logic**: Avoid if/else statements in tests - use parameterization instead
- **Consistent patterns**: Follow existing test conventions in the module
- **Proper ownership**: Mark tests as owned by safety-experience

### Test Structure
```python
import pytest

@pytest.mark.owners("safety-experience")
class TestFeatureName:
    def test_basic_functionality(self):
        # Arrange
        input_data = create_test_data()

        # Act
        result = function_under_test(input_data)

        # Assert
        assert result.expected_field == expected_value

    @pytest.mark.parametrize("input_val,expected", [
        (1, "success"),
        (0, "failure"),
        (-1, "error"),
    ])
    def test_edge_cases(self, input_val, expected):
        result = function_under_test(input_val)
        assert result.status == expected
```

## Instructions

### Step 1: Understand the code to test

Before writing tests, examine:

1. **The implementation**: Read the module/function being tested
2. **Existing tests**: Look for test files in the same module
   ```bash
   # Find existing test files
   find discord_api/discord/modules/MODULE_NAME -name "test_*.py" -o -name "*_test.py"
   ```

3. **Test patterns**: Read existing tests to understand conventions
   - How are fixtures used?
   - How are mocks created?
   - What assertion patterns are common?

### Step 2: Plan test coverage

Identify what to test (80/20 principle):

**Must test:**
- ✅ Core business logic and critical paths
- ✅ Edge cases that could cause bugs
- ✅ Error handling and validation
- ✅ Public API contracts

**Can skip:**
- ❌ Simple getters/setters
- ❌ Direct database queries (unless complex logic)
- ❌ Trivial utility functions
- ❌ Framework/library code

### Step 3: Check ownership configuration

Tests must be owned by safety-experience using one of these methods:

**Method 1: pytest.mark.owners decorator (preferred for new tests)**
```python
@pytest.mark.owners("safety-experience")
class TestMyFeature:
    def test_something(self):
        pass
```

**Method 2: owners.yaml file (check if exists)**
```bash
# Check for existing owners.yaml in the module
find discord_api/discord/modules/MODULE_NAME -name "owners.yaml"
```

If `owners.yaml` exists, add the test file path to it:
```yaml
- path: tests/test_my_feature.py
  owners:
    - safety-experience
```

### Step 4: Write tests following patterns

**Use parameterization for variations:**
```python
@pytest.mark.parametrize("user_age,is_teen,expected_allowed", [
    (12, True, False),   # Too young
    (13, True, True),    # Teen minimum age
    (17, True, True),    # Teen maximum age
    (18, False, True),   # Adult
])
def test_age_verification(user_age, is_teen, expected_allowed):
    result = verify_age_requirement(user_age, is_teen)
    assert result.allowed == expected_allowed
```

**Avoid logic in tests:**
```python
# ❌ BAD: Logic in test
def test_validation(self):
    for value in test_values:
        if value > 0:
            assert validate(value) is True
        else:
            assert validate(value) is False

# ✅ GOOD: Use parameterization
@pytest.mark.parametrize("value,expected", [
    (1, True),
    (0, False),
    (-1, False),
])
def test_validation(self, value, expected):
    assert validate(value) == expected
```

**Mock external dependencies:**
```python
from unittest.mock import Mock, patch

def test_api_call(self):
    mock_response = Mock(status_code=200, json={"result": "success"})

    with patch("requests.get", return_value=mock_response):
        result = fetch_data()
        assert result["result"] == "success"
```

**Use fixtures for common setup:**
```python
import pytest

@pytest.fixture
def test_user():
    return User(id=123, name="Test User", age=15)

@pytest.mark.owners("safety-experience")
class TestUserFeatures:
    def test_user_validation(self, test_user):
        assert validate_user(test_user) is True
```

### Step 5: Run tests

**Method 1: api-test-daemon (preferred)**

The api-test-daemon is faster for iterative testing but can become stale.

```bash
# IMPORTANT: Must be in discord_api directory
cd discord_api

# Run specific test file
./api-test-daemon discord/modules/MODULE_NAME/tests/test_file.py

# Run specific test class
./api-test-daemon discord/modules/MODULE_NAME/tests/test_file.py::TestClassName

# Run specific test method
./api-test-daemon discord/modules/MODULE_NAME/tests/test_file.py::TestClassName::test_method_name

# Run with verbose output
./api-test-daemon discord/modules/MODULE_NAME/tests/test_file.py -v

# Run with print statements visible
./api-test-daemon discord/modules/MODULE_NAME/tests/test_file.py -v -s
```

**Method 2: clyde api test (fallback)**

Use this if api-test-daemon is stale or having issues:

```bash
# Run from anywhere in the workspace
clyde api test discord/modules/MODULE_NAME/tests/test_file.py

# With verbose output
clyde api test discord/modules/MODULE_NAME/tests/test_file.py -v

# Run specific test
clyde api test discord/modules/MODULE_NAME/tests/test_file.py::TestClassName::test_method_name
```

### Step 6: Interpret results

**Successful test output:**
```
====== test session starts ======
collected 5 items

test_file.py::TestClass::test_method1 PASSED
test_file.py::TestClass::test_method2 PASSED

====== 5 passed in 0.23s ======
```

**Failed test output:**
```
====== FAILURES ======
____ TestClass.test_method ____

    def test_method(self):
>       assert result == expected
E       AssertionError: assert 'actual' == 'expected'

test_file.py:45: AssertionError
```

**Common issues:**
- Import errors → Check module paths and dependencies
- Fixture not found → Ensure fixture is defined or imported from conftest.py
- Stale daemon → Try clyde api test instead
- Database errors → Check test database setup and migrations

## Example Test Patterns

### Testing API endpoints
```python
@pytest.mark.owners("safety-experience")
class TestParentToolsEndpoint:
    def test_get_settings_success(self, test_client, auth_headers):
        response = test_client.get(
            "/api/v10/parent-tools/settings",
            headers=auth_headers
        )
        assert response.status_code == 200
        assert "settings" in response.json()

    @pytest.mark.parametrize("missing_field", ["user_id", "guild_id"])
    def test_missing_required_fields(self, test_client, missing_field):
        payload = {"user_id": 123, "guild_id": 456}
        del payload[missing_field]

        response = test_client.post("/api/v10/parent-tools/action", json=payload)
        assert response.status_code == 400
```

### Testing business logic
```python
@pytest.mark.owners("safety-experience")
class TestAgeVerification:
    def test_verify_teen_age_success(self):
        user = create_test_user(age=15)
        result = verify_teen_requirements(user)
        assert result.is_eligible is True

    @pytest.mark.parametrize("age,expected_eligible", [
        (12, False),
        (13, True),
        (17, True),
        (18, False),
    ])
    def test_age_boundaries(self, age, expected_eligible):
        user = create_test_user(age=age)
        result = verify_teen_requirements(user)
        assert result.is_eligible == expected_eligible
```

### Testing with database
```python
@pytest.mark.owners("safety-experience")
class TestUserRepository:
    def test_create_user(self, db_session):
        user_data = {"name": "Test", "age": 15}
        user = create_user(db_session, user_data)

        assert user.id is not None
        assert user.name == "Test"

        # Verify persisted
        db_session.refresh(user)
        assert user.age == 15
```

### Testing error handling
```python
@pytest.mark.owners("safety-experience")
class TestErrorHandling:
    def test_invalid_input_raises_error(self):
        with pytest.raises(ValidationError) as exc_info:
            process_invalid_data(None)

        assert "cannot be None" in str(exc_info.value)

    def test_handles_missing_data_gracefully(self):
        result = process_optional_data(None)
        assert result.status == "skipped"
        assert result.error is None
```

## Workflow Examples

### Scenario 1: Writing tests for new feature
```
User: "Write tests for the new parent controls feature"

1. Read the implementation files
2. Find existing tests in the module
3. Identify core logic to test (80/20)
4. Write test class with @pytest.mark.owners("safety-experience")
5. Use parameterization for edge cases
6. cd discord_api && ./api-test-daemon path/to/test_file.py -v
7. Fix any failures and iterate
```

### Scenario 2: Adding test coverage
```
User: "Add tests for the family center settings"

1. Read the settings module
2. Check existing test coverage
3. Identify gaps in coverage
4. Add new test methods to existing test class or create new class
5. Run tests: cd discord_api && ./api-test-daemon path/to/tests/ -v
6. Verify all pass
```

### Scenario 3: Debugging failing tests
```
User: "The tests are failing, help me fix them"

1. Run tests with verbose output: ./api-test-daemon path/to/test.py -v -s
2. If daemon seems stale, try: clyde api test path/to/test.py -v
3. Analyze failure messages
4. Fix the issues (code or test)
5. Re-run tests
```

## Important Notes

- **Always use @pytest.mark.owners("safety-experience")** for new test classes
- **Always cd to discord_api** before using api-test-daemon
- **Prefer api-test-daemon** for speed, but fallback to clyde api test if issues
- **Use parameterization** instead of loops or conditionals in tests
- **Follow existing patterns** in the module you're testing
- **Keep tests focused** - one logical assertion per test method
- **Mock external dependencies** - don't hit real APIs or databases unless necessary
- **Use descriptive test names** - test_should_do_x_when_y

## Common Pitfalls

### ❌ Don't do this:
```python
def test_multiple_scenarios(self):
    if condition:
        assert foo()
    else:
        assert bar()
```

### ✅ Do this instead:
```python
@pytest.mark.parametrize("condition,expected", [
    (True, "foo_result"),
    (False, "bar_result"),
])
def test_scenario(self, condition, expected):
    result = execute(condition)
    assert result == expected
```

## Troubleshooting

**"api-test-daemon not found"**
- Ensure you're in the discord_api directory
- Check if the file is executable: `ls -la api-test-daemon`

**"Daemon is stale / wrong results"**
- Fallback to: `clyde api test path/to/test.py`
- This rebuilds the test environment

**"Import errors"**
- Check that module paths are correct
- Ensure __init__.py files exist
- Verify test is in a tests/ directory

**"Fixture not found"**
- Check conftest.py files in parent directories
- Import fixture if defined in another file
- Define the fixture if missing

**"Tests pass locally but fail in CI"**
- Check for hardcoded values (timestamps, IDs)
- Ensure proper mocking of external services
- Verify test isolation (tests should not depend on each other)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zmchenry) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
