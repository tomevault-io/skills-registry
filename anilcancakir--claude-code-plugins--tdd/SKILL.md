---
name: tdd
description: | Use when this capability is needed.
metadata:
  author: anilcancakir
---

# Test-Driven Development Skill

Guidelines for effective Test-Driven Development.

## TDD Philosophy

> "Write a failing test before you write the code that makes it pass."
> - Kent Beck

## The TDD Cycle

```
    ┌─────────┐
    │   RED   │  Write failing test
    └────┬────┘
         │
    ┌────▼────┐
    │  GREEN  │  Write minimal code to pass
    └────┬────┘
         │
    ┌────▼────┐
    │REFACTOR │  Clean up, keep tests green
    └────┬────┘
         │
         └──────► Repeat
```

## RED Phase

### Goal
Write a test that FAILS because the code does not exist yet.

### Rules
- Test expresses expected behavior
- Test is focused on ONE thing
- Test has meaningful assertions
- Test MUST fail when run

### If Test Passes in RED
Something is wrong:
- Code may already exist
- Test may be incorrectly written
- Test may not be testing what you think

**Investigate before proceeding.**

## GREEN Phase

### Goal
Write the MINIMUM code to make the test pass.

### Rules
- Only write enough to pass the test
- No optimization
- No additional features
- No "while I'm here" changes
- Ugly code is OK

### Mantra
"Make it work, make it right, make it fast."
(We're in "make it work" phase)

## REFACTOR Phase

### Goal
Improve code quality while keeping tests green.

### Rules
- Run tests after EVERY change
- Small, incremental improvements
- Revert if tests fail
- Stop when code is clean enough

### Common Refactorings
- Extract method/function
- Rename for clarity
- Remove duplication
- Apply design patterns
- Add documentation

## Framework-Specific Guides

- [Laravel Testing](references/laravel-testing.md)
- [Flutter Testing](references/flutter-testing.md)
- [Vue Testing](references/vue-testing.md)

## When to Use TDD

### Always Use TDD For
- Business logic
- Data transformations
- Validation rules
- API endpoints
- Services

### Consider Skipping TDD For
- Simple CRUD (test at integration level)
- Configuration code
- Boilerplate/scaffolding
- UI-only changes (use manual testing)

## Test Types

| Type | Tests | Speed | Confidence |
|------|-------|-------|------------|
| Unit | Single class/function | Fast | Low |
| Integration | Multiple components | Medium | Medium |
| Feature/E2E | Full flow | Slow | High |

## Test Pyramid

```
        /\
       /  \      E2E Tests (few)
      /----\
     /      \    Integration Tests (some)
    /--------\
   /          \  Unit Tests (many)
  /____________\
```

## Coverage Targets

| Area | Minimum | Target |
|------|---------|--------|
| Business Logic | 90% | 100% |
| Services | 85% | 95% |
| Controllers | 80% | 90% |
| UI Components | 70% | 85% |

## Anti-Patterns

### Test After
Writing tests after implementation - you lose the design benefits of TDD.

### Testing Implementation
Testing HOW code works instead of WHAT it does.

### Over-Mocking
Mocking everything - you're testing your mocks, not your code.

### Giant Tests
Tests that test too much - hard to debug when they fail.

### Flaky Tests
Tests that sometimes pass, sometimes fail - erode trust in test suite.

## Quick Reference

### RED Phase Checklist
- [ ] Test has descriptive name
- [ ] Test is focused on ONE behavior
- [ ] Test has clear assertion
- [ ] Test FAILS when run

### GREEN Phase Checklist
- [ ] Code is minimal
- [ ] No extra features
- [ ] Test PASSES
- [ ] No new tests written

### REFACTOR Phase Checklist
- [ ] Code is cleaner
- [ ] Tests still PASS
- [ ] No behavior changed
- [ ] Ready for next cycle

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anilcancakir) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
