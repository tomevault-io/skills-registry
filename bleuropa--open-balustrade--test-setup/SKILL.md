---
name: test-setup
description: Setting up tests for new code covering unit tests, integration tests, and test organization Use when this capability is needed.
metadata:
  author: bleuropa
---

# Test Setup Skill

Guidance for writing effective tests, organizing test files, and establishing good testing practices.

## When To Use

- Adding tests to new features
- Setting up test infrastructure
- Improving test coverage
- Learning testing patterns

## Test Types

### Unit Tests

**What**: Test individual functions/classes in isolation
**When**: Pure logic, utilities, business rules
**Speed**: Fast (milliseconds)
**Dependencies**: Mock external dependencies

```javascript
describe('calculateDiscount', () => {
  it('applies 10% discount for premium users', () => {
    const result = calculateDiscount(100, 'premium')
    expect(result).toBe(90)
  })

  it('returns original price for regular users', () => {
    const result = calculateDiscount(100, 'regular')
    expect(result).toBe(100)
  })
})
```

### Integration Tests

**What**: Test multiple components working together
**When**: API endpoints, database operations, service interactions
**Speed**: Slower (seconds)
**Dependencies**: Use real dependencies or test doubles

```javascript
describe('POST /api/users', () => {
  it('creates user and returns 201', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ name: 'Test User', email: 'test@example.com' })

    expect(response.status).toBe(201)
    expect(response.body.user).toMatchObject({
      name: 'Test User',
      email: 'test@example.com'
    })
  })
})
```

### End-to-End Tests

**What**: Test complete user workflows
**When**: Critical user paths, full stack verification
**Speed**: Slowest (seconds to minutes)

## Test Organization

### File Structure

```
your-project/
├── src/
│   ├── utils.js
│   └── api/users.js
├── tests/
│   ├── unit/
│   │   └── utils.test.js
│   ├── integration/
│   │   └── api/users.test.js
│   └── e2e/
│       └── checkout.spec.js
```

### Test Structure (AAA Pattern)

```javascript
test('descriptive test name', () => {
  // Arrange - Set up test data
  const user = { id: 1, name: 'Test' }

  // Act - Execute the code
  const result = service.formatUser(user)

  // Assert - Verify the results
  expect(result).toBe('User: Test')
})
```

## Writing Good Tests

### Good Test Names

```javascript
// Bad
test('it works', ...)
test('test user', ...)

// Good
test('returns 404 when user not found', ...)
test('calculates total including tax', ...)
```

### Test One Thing

Separate tests for each behavior, not one giant test.

### Test Edge Cases

```javascript
describe('divide', () => {
  it('divides two positive numbers', () => {
    expect(divide(10, 2)).toBe(5)
  })

  it('throws when dividing by zero', () => {
    expect(() => divide(10, 0)).toThrow('Division by zero')
  })
})
```

## Mocking

### When to Mock

**Mock**:
- API calls
- Database queries
- External services
- Time/dates

**Don't mock**:
- Code you're testing
- Simple utilities
- Your own business logic

## Test Coverage

### What to Aim For

- Critical paths: 100% coverage
- Business logic: 90%+ coverage
- Utilities: 80%+ coverage
- UI components: Behavior over coverage %

### Coverage is not Quality

Good: High coverage with meaningful assertions
Bad: High coverage with weak assertions

## Checklist

When writing tests:
- [ ] Test name describes what's being tested
- [ ] Test is focused (tests one thing)
- [ ] Edge cases covered
- [ ] Assertions are meaningful
- [ ] External dependencies mocked
- [ ] Tests are independent (no shared state)
- [ ] Tests run fast

## Remember

- Tests are documentation
- Good tests catch bugs before production
- Fast tests encourage running them often
- Flaky tests erode confidence
- Tests should be as simple as possible

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bleuropa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
