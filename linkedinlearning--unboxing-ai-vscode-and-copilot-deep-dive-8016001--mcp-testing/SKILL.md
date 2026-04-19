---
name: mcp-testing
description: Generate and manage comprehensive test suites for MCP tools with coverage reporting Use when this capability is needed.
metadata:
  author: linkedinlearning
---

# MCP Testing Skill

## Overview

The MCP Testing Skill automates test generation for Model Context Protocol tools. It creates comprehensive test suites with happy path, error cases, edge cases, and parameter validation tests, ensuring robust coverage.

## When to Use This Skill

- **Scenario 1**: Generating initial test suite for a new tool
- **Scenario 2**: Expanding test coverage for existing tools
- **Scenario 3**: Adding error case testing
- **Scenario 4**: Creating integration tests combining multiple tools
- **Scenario 5**: Performance and load testing verification

## Key Capabilities

### 1. Test Generation

- Happy path test creation
- Error case test generation
- Parameter validation tests
- Edge case identification and testing
- Integration test scaffolding

### 2. Mock Management

- API response mocking setup
- Environment variable mocking
- Fixture creation for test data
- Mock response libraries (httpx, etc.)
- Realistic test data generation

### 3. Coverage Analysis

- Code path coverage measurement
- Coverage reporting (HTML, text, JSON)
- Gap identification
- Coverage target enforcement
- Regression detection

### 4. Test Execution

- Test running and reporting
- Failure analysis
- Performance profiling
- Concurrent execution
- CI/CD integration

### 5. Test Organization

- Test file structure creation
- Test naming conventions
- Fixture and helper organization
- Documentation generation

## Quick Start

### Basic Usage

```
Request: "Generate tests for the get_current_weather tool"
[Provide tool code]
Skill: Analyzes tool and generates comprehensive test suite
Output: Test file with 5-8 test cases covering all scenarios
```

### Advanced Usage

```
Request: "Create integration tests for all weather tools"
[Provide multiple tool definitions]
Skill: Generates combined tool tests plus integration scenarios
Output: test_tools_integration.py with cross-tool tests
```

## Test Coverage Plan

### Test Categories

#### 1. Happy Path Tests (40% of tests)

```python
✓ test_get_current_weather_valid_coordinates
✓ test_get_current_weather_different_units
✓ test_get_current_weather_expected_fields_present
```

#### 2. Parameter Validation Tests (30% of tests)

```python
✓ test_get_current_weather_latitude_too_high
✓ test_get_current_weather_latitude_too_low
✓ test_get_current_weather_invalid_units
```

#### 3. Error Handling Tests (20% of tests)

```python
✓ test_get_current_weather_api_timeout
✓ test_get_current_weather_api_error
✓ test_get_current_weather_network_error
```

#### 4. Edge Case Tests (10% of tests)

```python
✓ test_get_current_weather_boundary_values
✓ test_get_current_weather_special_characters
```

## Test Template

```python
import pytest
from unittest.mock import AsyncMock, patch

@pytest.mark.asyncio
async def test_<tool_name>_<scenario>():
    """
    Test [tool_name] for [scenario].

    This test verifies:
    - [What is being tested]
    - [Why it matters]
    """
    # Arrange: Setup test data and mocks
    mock_response = {...}
    expected_result = {...}

    with patch("httpx.AsyncClient.get") as mock_get:
        mock_get.return_value.json.return_value = mock_response
        mock_get.return_value.raise_for_status = AsyncMock()

        # Act: Call the tool
        result = await get_current_weather(lat=51.5, lon=-0.1)

        # Assert: Verify results
        assert result == expected_result
        assert "error" not in result
        mock_get.assert_called_once()
```

## Test Organization Structure

```
tests/
├── conftest.py              # Shared fixtures, mocks
├── test_tools_weather.py    # Weather tool tests
├── test_tools_location.py   # Location tool tests
├── test_tools_integration.py # Combined tool tests
├── test_tools_performance.py # Performance tests
└── fixtures/
    ├── weather_responses.json
    ├── location_responses.json
    └── error_responses.json
```

## Coverage Goals

| Category    | Target | Why                     |
| ----------- | ------ | ----------------------- |
| Overall     | 80%+   | Catch most issues       |
| Tool Logic  | 90%+   | All paths covered       |
| Error Paths | 85%+   | Error handling verified |
| Integration | 70%+   | Combined scenarios work |

## Test Execution

### Run All Tests

```bash
pytest tests/ -v --cov=src --cov-report=html
```

### Run Specific Test

```bash
pytest tests/test_tools_weather.py::test_get_current_weather_valid_coordinates -v
```

### Generate Coverage Report

```bash
pytest tests/ --cov=src --cov-report=term-missing
```

## Mocking Strategies

### Strategy 1: Mock External APIs

```python
with patch("httpx.AsyncClient.get") as mock_get:
    mock_get.return_value.json.return_value = {"temp": 20}
    result = await get_current_weather(51.5, -0.1)
```

### Strategy 2: Mock Environment Variables

```python
with patch.dict(os.environ, {"OPENWEATHERMAP_API_KEY": "test-key"}):
    result = await get_current_weather(51.5, -0.1)
```

### Strategy 3: Fixture-Based Responses

```python
@pytest.fixture
def mock_weather_response():
    return {
        "main": {"temp": 20},
        "weather": [{"main": "Cloudy"}]
    }
```

## Test Quality Metrics

### Test Quality Checklist

- [ ] Each test tests ONE thing
- [ ] Test names clearly describe what they test
- [ ] Assertions are specific (not generic)
- [ ] Mocks are realistic and complete
- [ ] Tests run in <1 second each
- [ ] No interdependencies between tests
- [ ] All assertions have meaningful failure messages

### Bad vs Good Tests

**❌ Bad Test**

```python
def test_weather():  # Vague name
    result = get_current_weather(0, 0)
    assert result  # Generic assertion
```

**✓ Good Test**

```python
@pytest.mark.asyncio
async def test_get_current_weather_valid_coordinates_success():
    """Get weather succeeds with valid lat/lon coordinates."""
    result = await get_current_weather(lat=51.5, lon=-0.1)

    assert "error" not in result, "Valid coordinates should not produce errors"
    assert result["temperature"] > -100, "Temperature should be reasonable"
    assert result["humidity"] >= 0 and result["humidity"] <= 100
```

## Performance Testing

### Response Time Tests

```python
@pytest.mark.asyncio
async def test_get_current_weather_performance():
    """Tool should respond within 5 seconds."""
    import time

    start = time.time()
    result = await get_current_weather(51.5, -0.1)
    elapsed = time.time() - start

    assert elapsed < 5.0, f"Tool took {elapsed}s, expected <5s"
    assert "error" not in result
```

## Integration Testing

### Combined Tool Testing

```python
@pytest.mark.asyncio
async def test_weather_for_location_integration():
    """Test search + weather combination."""
    # This combines two tools
    result = await weather_for_location("London")

    assert "error" not in result
    assert "location" in result
    assert "temperature" in result
```

## Test Naming Convention

```
test_<tool_name>_<scenario>_<expectation>

Examples:
test_get_current_weather_valid_coordinates_success
test_get_current_weather_latitude_out_of_range_error
test_search_location_empty_query_returns_error
test_weather_for_location_integration_combines_results
```

## Continuous Integration

### CI/CD Integration

```yaml
# .github/workflows/test.yml
name: Tests
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-python@v2
        with:
          python-version: "3.11"
      - run: pip install -r requirements.txt
      - run: pytest tests/ --cov=src --cov-report=xml
      - uses: codecov/codecov-action@v2
```

## Test Reporting

### Coverage Report Example

```
name                    stmts   miss  cover
────────────────────────────────────────
src/server/index.py       120     5   96%
src/tools/weather.py       45     2   95%
src/tools/location.py      38     3   92%
────────────────────────────────────────
TOTAL                     203    10   95%
```

## Common Test Issues

### Issue: Tests are flaky (sometimes pass/fail)

**Cause**: Timing issues or incomplete mocks
**Fix**: Ensure all async operations are awaited; mock all external calls

### Issue: Mock data unrealistic

**Cause**: Test data doesn't match real API responses
**Fix**: Capture real API responses and use as fixtures

### Issue: Tests take too long

**Cause**: Actually calling external APIs or slow assertions
**Fix**: Mock all I/O; use fixtures; check for N+1 queries

### Issue: Low coverage despite many tests

**Cause**: Not testing error paths or edge cases
**Fix**: Systematically add tests for each error condition

## Test Maintenance

### Keeping Tests Updated

- Update tests when tool signature changes
- Add tests for each bug found
- Refresh mock data when APIs change format
- Periodically review test coverage

### Test Review Checklist

- [ ] All new code has tests
- [ ] Coverage remains 80%+
- [ ] All error paths tested
- [ ] Performance acceptable
- [ ] Mocks are current and realistic

## Reference Materials

- `.github/instructions/testing.instructions.md` - Testing standards
- `.github/copilot/exemplars.md` - Example implementations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/linkedinlearning) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
