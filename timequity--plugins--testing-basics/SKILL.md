---
name: testing-basics
description: | Use when this capability is needed.
metadata:
  author: timequity
---

# Testing Basics

Core testing patterns that apply to any language or framework.

## Test Structure

### AAA Pattern
```
Arrange → Act → Assert
```

```python
# Arrange
user = User(name="Alice")

# Act
result = user.greet()

# Assert
assert result == "Hello, Alice!"
```

## Test Types

| Type | Scope | Speed | When to use |
|------|-------|-------|-------------|
| Unit | Single function/class | Fast | Core logic |
| Integration | Multiple components | Medium | APIs, DB |
| E2E | Full system | Slow | Critical paths |

## Naming Tests

```
test_[what]_[condition]_[expected]
```

Examples:
- `test_login_valid_credentials_returns_token`
- `test_checkout_empty_cart_raises_error`
- `test_search_no_results_returns_empty_list`

## What to Test

### Do Test
- Business logic
- Edge cases
- Error handling
- Public interfaces

### Don't Test
- Framework code
- Simple getters/setters
- Implementation details
- External services directly

## Test Isolation

Each test should:
- Run independently
- Not depend on order
- Clean up after itself
- Not share state

## Mocking

Mock external dependencies, not internal logic:

```python
# Good: mock external service
@patch('app.services.email.send')
def test_signup_sends_welcome_email(mock_send):
    signup(user)
    mock_send.assert_called_once()

# Bad: mock internal implementation
@patch('app.models.user.User._validate')  # Don't do this
```

## Coverage

- Aim for meaningful coverage, not 100%
- Cover happy paths first
- Then error cases
- Then edge cases

## Quick Checklist

Before committing:
- [ ] Tests pass locally
- [ ] New code has tests
- [ ] No flaky tests added
- [ ] Test names are descriptive

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/timequity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
