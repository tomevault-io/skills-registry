---
name: tdd-workflow
description: Use when working with a structured methodology for writing tests before code to ensure high quality and reliability.
metadata:
  author: kuwa72
---
# Skill: TDD Workflow

This skill enforces a discipline of writing automated tests before implementation code.

## 🔄 The Red-Green-Refactor Cycle
1. **RED**: Write a failing unit test for a specific requirement.
2. **GREEN**: Write the minimal amount of implementation code to make the test pass.
3. **REFACTOR**: Clean up the implementation and the test code while keeping the test passing.

## 📋 Coverage Targets
- Aim for at least **80% code coverage** for business logic and utilities.
- Focus tests on behavior, not implementation details.

## 🛠️ Tooling
- **Frontend**: Vitest / Jest / Playwright.
- **Backend**: Rust's built-in `#[test]` and `tokio::test`.

## 📄 Example: TDD Step
```typescript
// 1. Red (Test)
test('add(1, 2) returns 3', () => {
  expect(add(1, 2)).toBe(3); // Fails: add is undefined
});

// 2. Green (Code)
const add = (a, b) => a + b;

// 3. Refactor
// (Optimize or clean up name if needed)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kuwa72) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
