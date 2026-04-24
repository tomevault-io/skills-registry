---
name: test-auto
description: Create unit, integration, and e2e test suites. Use when setting up or improving tests. Use when this capability is needed.
metadata:
  author: htlin222
---

# Test Automation

Create comprehensive test suites following the testing pyramid.

## When to Use

- Setting up tests for new code
- User asks to "add tests" or "improve coverage"
- Before refactoring (add tests first)
- Implementing CI/CD test pipelines

## Testing Pyramid

```
    /\        E2E (few, critical paths)
   /  \       Integration (moderate)
  /____\      Unit (many, fast)
```

## Test Structure

### Unit Tests

- Test individual functions/methods
- Mock external dependencies
- Fast execution (<100ms per test)
- High coverage (>80%)

### Integration Tests

- Test component interactions
- Use test databases/containers
- Moderate execution time
- Cover critical integrations

### E2E Tests

- Test complete user flows
- Use Playwright/Cypress
- Slowest execution
- Cover happy paths only

## Test Patterns

```javascript
// Arrange-Act-Assert
describe("UserService", () => {
  it("should create user with valid data", async () => {
    // Arrange
    const userData = { name: "Test", email: "test@example.com" };

    // Act
    const result = await userService.create(userData);

    // Assert
    expect(result.id).toBeDefined();
    expect(result.name).toBe("Test");
  });
});
```

## Output

- Test files with clear naming
- Mock/stub implementations
- Test data factories
- Coverage configuration
- CI pipeline integration

## Examples

**Input:** "Add tests for the auth module"
**Action:** Analyze auth module, create unit tests for functions, integration tests for flows

**Input:** "Set up testing for this project"
**Action:** Detect framework, configure test runner, create example tests, add CI config

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/htlin222) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
