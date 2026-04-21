---
name: testing-patterns
description: >- Use when this capability is needed.
metadata:
  author: dicklesworthstone
---

# Testing Patterns

Foundational testing practices applicable across languages and frameworks.

## Rules

- Write tests for happy paths and error cases
- Test edge cases and boundary conditions
- Each test should test one thing
- Tests should be deterministic and repeatable
- Use descriptive test names that explain the scenario

## Checklist

- [ ] Happy path is covered
- [ ] Error cases are tested
- [ ] Edge cases are identified and tested
- [ ] Tests are independent and can run in any order
- [ ] Test data is isolated per test
- [ ] No flaky tests in the suite

## Pitfalls

- Testing implementation details instead of behavior
- Not testing error paths
- Shared mutable state between tests
- Tests that depend on execution order

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dicklesworthstone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
