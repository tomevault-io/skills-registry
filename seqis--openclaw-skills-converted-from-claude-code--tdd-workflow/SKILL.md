---
name: tdd-workflow
description: Test-driven development workflow with Red-Green-Refactor cycle Use when this capability is needed.
metadata:
  author: seqis
---

# TDD Workflow Skill

## Overview

Test-Driven Development workflow enforcement. This skill ensures code is written test-first, following the Red-Green-Refactor cycle for higher quality and fewer bugs.

## Type

technique

## When to Invoke

**Trigger keywords:** TDD, test-driven, tests first, test coverage, red-green-refactor, write tests

**Trigger phrases:**
- "write tests for..."
- "test-driven development"
- "implement with TDD"
- "tests first approach"

## Core Mandate

**Write the test BEFORE the implementation.**

This is not optional. TDD produces better designs because:
- Forces thinking about interface before implementation
- Catches edge cases early
- Creates documentation through tests
- Enables safe refactoring

## The Red-Green-Refactor Cycle

```
┌─────────────────────────────────────────┐
│                                         │
│    ┌─────┐      ┌───────┐      ┌──────┐ │
│    │ RED │ ───► │ GREEN │ ───► │REFAC-│ │
│    │     │      │       │      │ TOR  │ │
│    └─────┘      └───────┘      └──────┘ │
│       ▲                           │     │
│       └───────────────────────────┘     │
│                                         │
└─────────────────────────────────────────┘
```

### 1. RED: Write a Failing Test

**Before writing ANY code:**
1. Define expected behavior
2. Write test that expresses this behavior
3. Run test - it MUST fail
4. If test passes, you wrote the wrong test

```python
# Example: Testing a function that doesn't exist yet
def test_add_numbers_returns_sum():
    result = add_numbers(2, 3)  # Function doesn't exist!
    assert result == 5
```

### 2. GREEN: Write Minimal Code to Pass

**Write ONLY enough code to make the test pass:**
- No extra features
- No optimizations
- No "while I'm here" additions
- Ugly code is fine (for now)

```python
# Minimal implementation - just enough to pass
def add_numbers(a, b):
    return a + b
```

### 3. REFACTOR: Clean Up While Tests Pass

**Now improve the code:**
- Remove duplication
- Improve naming
- Extract methods/functions
- Run tests after EVERY change
- If tests fail, you broke something - revert

## Test Pyramid

```
          ┌──────────────┐
         /   E2E Tests    \     ← Few, slow, fragile
        /   (10% of tests)  \
       ├────────────────────┤
      /  Integration Tests    \  ← Some, moderate speed
     /    (20% of tests)        \
    ├────────────────────────────┤
   /      Unit Tests               \ ← Many, fast, stable
  /       (70% of tests)             \
 └─────────────────────────────────────┘
```

### Unit Tests (70%)
- Test single function/method
- No external dependencies (DB, network, filesystem)
- Fast (< 100ms each)
- Use mocks for dependencies

### Integration Tests (20%)
- Test component interactions
- May use test databases
- Moderate speed (< 5s each)
- Verify real integrations work

### E2E Tests (10%)
- Test full user flows
- Use real (or realistic) environment
- Slow but comprehensive
- Catch integration issues

## Coverage Requirements

| Coverage Type | Minimum | Target |
|--------------|---------|--------|
| Line coverage | 70% | 80%+ |
| Branch coverage | 60% | 70%+ |
| Critical paths | 100% | 100% |
| Edge cases | Explicit | Complete |

**Critical paths requiring 100% coverage:**
- Authentication/authorization
- Payment processing
- Data validation
- Security-sensitive code

## TDD Iteration Tracking

Track each TDD cycle (similar to Ralph technique):

| Cycle | Test Written | Implementation | Refactoring | Notes |
|-------|--------------|----------------|-------------|-------|
| 1 | Basic happy path | Minimal impl | None yet | Foundation |
| 2 | Edge case: empty input | Handle empty | Extract validation | |
| 3 | Edge case: invalid type | Type checking | Clean up | |

## Test Patterns

### Arrange-Act-Assert (AAA)
```python
def test_user_can_login():
    # Arrange: Set up test data
    user = create_test_user(email="test@example.com", password="secret")

    # Act: Perform the action
    result = login(email="test@example.com", password="secret")

    # Assert: Verify the outcome
    assert result.success is True
    assert result.user.email == "test@example.com"
```

### Given-When-Then (BDD style)
```python
def test_user_login():
    # Given a registered user
    user = create_test_user()

    # When they login with correct credentials
    result = login(user.email, user.password)

    # Then they should be authenticated
    assert result.is_authenticated()
```

## Mocking Strategy

**When to mock:**
- External services (APIs, databases)
- Time-dependent code
- Random/non-deterministic behavior
- Slow operations

**When NOT to mock:**
- The unit under test
- Simple value objects
- Pure functions

```python
# Good: Mock external dependency
@patch('myapp.services.external_api.fetch')
def test_fetches_data(mock_fetch):
    mock_fetch.return_value = {"data": "test"}
    result = process_external_data()
    assert result == "processed: test"

# Bad: Mocking the thing you're testing
# Don't do this - tests nothing!
```

## Edge Cases Checklist

Always test:
- [ ] Empty input
- [ ] Null/None values
- [ ] Boundary values (0, -1, MAX_INT)
- [ ] Invalid types
- [ ] Very large inputs
- [ ] Unicode/special characters
- [ ] Concurrent access (if applicable)
- [ ] Network failures (if applicable)
- [ ] Timeout scenarios

## Anti-Patterns

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| Test after code | Doesn't drive design | Always test first |
| Testing implementation | Brittle tests | Test behavior only |
| No assertions | Test passes but proves nothing | Always assert |
| Ignoring failing tests | Technical debt | Fix or delete |
| Over-mocking | Tests pass, code broken | Mock boundaries only |
| Shared test state | Flaky tests | Isolate each test |

## Integration with /newapp

When `/newapp` invokes this skill:
1. Test framework setup included in project scaffold
2. Example tests created
3. Coverage configuration added
4. CI pipeline includes test stage

## Integration

Works with:
- `project-scaffolding` - Test structure in new projects
- `systematic-debugging` - Tests catch regressions
- `/newapp` command - Invokes during implementation phase
- `/fix` command - Write failing test before fix

---

*Adapted from TDD principles by Kent Beck*
*Anthropic-recommended workflow for Claude Code*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seqis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
