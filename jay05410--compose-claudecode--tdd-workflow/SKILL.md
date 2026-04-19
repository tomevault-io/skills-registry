---
name: tdd-workflow
description: Test-driven development with 80%+ coverage. Use when this capability is needed.
metadata:
  author: jay05410
---

# TDD Workflow

## Cycle

1. **RED** - Write failing test
2. **GREEN** - Write minimal code to pass
3. **REFACTOR** - Improve without changing behavior
4. **REPEAT**

## Test Types

| Type | Purpose | Tools |
|------|---------|-------|
| Unit | Functions, utilities | vitest, jest, pytest, go test |
| Integration | API endpoints, DB | supertest, httpx |
| E2E | User flows | Playwright |

## Coverage

- Minimum: 80%
- Focus: Critical paths, edge cases, error handling

## Test Pattern

```
describe('feature', () => {
  it('does expected behavior', () => {
    // Arrange
    const input = setupTestData()
    
    // Act
    const result = functionUnderTest(input)
    
    // Assert
    expect(result).toBe(expected)
  })
  
  it('handles edge case', () => { ... })
  it('handles error case', () => { ... })
})
```

## Mocking

Mock external dependencies (DB, APIs, file system) to isolate unit tests.

## Anti-Patterns

- Testing implementation details (test behavior, not internals)
- Brittle selectors (use data-testid or semantic selectors)
- Tests depending on each other (each test should be independent)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jay05410) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
