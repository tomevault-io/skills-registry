---
name: testing
description: Use when writing or modifying tests, fixing bugs with TDD, reviewing test code, or when user mentions "test", "tests", "testing", "TDD", "test-driven", "pytest", "add tests", "write tests", "unit test", "integration test", "test coverage", "bug fix", "fix bug", "verify", "edge case", "mocking", "patch.object".
metadata:
  author: gigaverse-app
---

# Testing Best Practices

## Core Philosophy

**Write invariant-based tests that verify what SHOULD be true, not bug-affirming tests that prove bugs existed.**

## Test-Driven Development (TDD)

**ALWAYS use TDD when fixing bugs:**

1. Find existing tests for the broken functionality
2. Run them to verify they pass (shouldn't catch bug)
3. Improve tests until they fail (exposing the bug)
4. Fix the code to make tests pass
5. Verify all tests pass

## Quick Start

```bash
# Look for existing fixtures in conftest.py
grep -r "@pytest.fixture" tests/conftest.py

# Look at sibling test files for patterns
ls tests/test_<module_name>/

# Run tests with coverage
pytest --cov=src --cov-report=term-missing
```

## Critical Rules Quick Reference

### Mocking

```python
# ALWAYS use patch.object
@patch.object(MyClass, 'method_name')

# ALWAYS mock dependencies, NEVER the system under test
generator = NewsPostGenerator()
generator._queries_chain = AsyncMock()  # Mock dependency
await generator.generate_news_post(...)  # Test actual code
```

### Test Data

```python
# ALWAYS use Pydantic models
return UserResult(user_id=user_id, name="Test User", ...)

# NEVER use naked literals - extract constants
DEFAULT_TIMEOUT = 60
STALE_THRESHOLD = DEFAULT_TIMEOUT + 10
```

### Invariant Testing

```python
# Test what SHOULD be true
def test_selector_populated_with_all_names():
    """INVARIANT: Selector contains all names from config."""
    config = make_config_with_items(["item1", "item2", "item3"])
    page = setup_page_with_config(config)
    assert page.item_selector.options == ["item1", "item2", "item3"]
```

### E2E Testing

```python
# Call actual production code
await service.process_request(request_input)
published_event = mock_queue.publish.call_args.kwargs["output"]
assert published_event.data is not None
```

### Intent & Testability

```python
# Extract pure functions from infrastructure code
def apply_suffix(user_id: str, name: str, is_special: bool) -> tuple[str, str]:
    """Pure function - easily testable without mocks."""
    if is_special:
        return f"{user_id}_special", f"{name}_special"
    return user_id, name

# Test behavior (inputs -> outputs), not implementation
@pytest.mark.parametrize("user_id,name,is_special,expected_id,expected_name", [
    ("alice", "Alice", True, "alice_special", "Alice_special"),
    ("alice", "Alice", False, "alice", "Alice"),
])
def test_suffix_behavior(user_id, name, is_special, expected_id, expected_name):
    result_id, result_name = apply_suffix(user_id, name, is_special)
    assert result_id == expected_id

# NEVER write sham tests that mock __init__ and set internal state
# They test the mock setup, not production code
```

## Testing Checklist

Before committing:
- [ ] All imports at top of file
- [ ] Using `patch.object`, not `patch`
- [ ] No mocking of the system under test
- [ ] No mocking of model classes
- [ ] Test data uses Pydantic models
- [ ] Checked conftest.py for existing fixtures
- [ ] No naked literals - all values are constants
- [ ] Using correct fixture decorators
- [ ] Asserting invariants early
- [ ] Mock args accessed with `.args[N]` / `.kwargs["name"]`
- [ ] E2E tests call actual production code
- [ ] Tests verify invariants, not bug existence
- [ ] 100% coverage of new code
- [ ] All tests pass

## Reference Files

For detailed patterns and examples:
- [references/mocking.md](references/mocking.md) - Mocking strategies and when to mock
- [references/test-data.md](references/test-data.md) - Test data creation patterns
- [references/e2e-testing.md](references/e2e-testing.md) - E2E and user journey patterns
- [references/intent-and-testability.md](references/intent-and-testability.md) - Intent documentation, pure functions, testability
- [references/concurrency.md](references/concurrency.md) - Concurrency testing patterns
- [references/fixtures.md](references/fixtures.md) - Common fixtures and decorators

**Remember**: When user reports bug, use TDD - find tests -> run -> improve until fail -> fix code -> verify pass.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gigaverse-app) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
