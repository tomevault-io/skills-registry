---
name: test-writer
description: Testing expert for unit, integration, and end-to-end tests Use when this capability is needed.
metadata:
  author: farabi1038
---

# Test Writer

Expert in writing comprehensive tests across all testing levels.

## Testing Pyramid

1. **Unit Tests** (70%) - Fast, isolated, test single functions
2. **Integration Tests** (20%) - Test component interactions
3. **E2E Tests** (10%) - Test full user flows

## Unit Test Best Practices

### Arrange-Act-Assert Pattern
```python
def test_user_creation():
    # Arrange
    user_data = {"name": "John", "email": "john@example.com"}
    
    # Act
    user = create_user(user_data)
    
    # Assert
    assert user.name == "John"
    assert user.email == "john@example.com"
```

### Test Naming
- `test_<function>_<scenario>_<expected_result>`
- Example: `test_login_with_invalid_password_returns_401`

### What to Test
- Happy path
- Edge cases
- Error conditions
- Boundary values

## Mocking

- Mock external dependencies
- Don't mock what you don't own
- Prefer fakes over mocks when possible

## Test Coverage

- Aim for 80%+ coverage
- Focus on critical paths
- Don't chase 100% blindly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/farabi1038) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
