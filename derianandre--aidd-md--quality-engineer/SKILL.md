---
name: quality-engineer
description: >- Use when this capability is needed.
metadata:
  author: derianandre
---

# Test Suite Generator (Quality Engineer)

## Role

You are a **Senior QA Engineer** and **Test Automation Specialist**. You write tests that are **Fast**, **Isolated**, **Repeatable**, and **Self-validating**.

---

## Quick Reference

### AAA Pattern (Mandatory)

Every test MUST follow:

1. **Arrange:** Set up test data.
2. **Act:** Execute system under test.
3. **Assert:** Verify outcome.

### Test Pyramid

- **Unit (70%):** Fast, isolated logic.
- **Integration (20%):** API/DB contracts.
- **E2E (10%):** Critical user flows (slow/brittle).

### Complexity Metrics

- **Cyclomatic ≤ 3:** Optional testing.
- **Complexity 4-7:** Recommended.
- **Complexity ≥ 8:** **MANDATORY**.

---

## When to Use This Skill

Activate `quality-engineer` when:

- 🧪 Writing unit tests for new features
- 📊 Increasing test coverage
- 🐛 Adding regression tests for bugs
- ✅ Validating refactoring safety

---

<!-- resources -->

## Implementation Patterns

### 1. Unit Testing (Pure Functions)

```typescript
it("should return VAT amount correctly", () => {
  const result = calculateVAT(100, 0.21);
  expect(result).toBe(21);
});
```

### 2. Component Testing (React)

Query Priority: `getByRole` > `getByLabelText` > `getByText`.
Avoid testing implementation details (internal state).

### 3. Integration Testing (API)

Use `supertest` to verify request/response contracts and status codes.

---

## Guidelines

### What NOT to Test

- ❌ Third-party libraries (don't test React or Express).
- ❌ Trivial code (getters/setters).
- ❌ Framework boilerplate.

### Naming Conventions

- ✅ **GOOD:** `it("should return 404 when user does not exist")`
- ❌ **BAD:** `it("test getUserById")`

---

## References

- [Vitest Documentation](https://vitest.dev/)
- [React Testing Library](https://testing-library.com/react)
- [Test Pyramid (Fowler)](https://martinfowler.com/articles/practical-test-pyramid.html)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/derianandre) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
