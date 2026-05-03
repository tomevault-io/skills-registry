---
name: tdd-enforce
description: Enforce test-driven development workflow. Use when implementing any new feature or fixing a bug — write the test first, verify it fails, then implement. Use when this capability is needed.
metadata:
  author: nbosiacki
---

# TDD Enforce

Follow strict Red-Green-Refactor for every implementation task.

## Workflow

### 1. Red — Write the Failing Test
- Create the test file in the correct mirror location (e.g., `app/services/user.py` → `tests/unit/services/test_user.py`).
- Add a module-level docstring to the test file describing what it tests.
- Write a test that describes the desired behavior. Use descriptive names following project conventions.
- Run the test. **It must fail.** If it passes, the test isn't testing anything new — revise it.
- Show the user the failing test output.

### 2. Green — Minimal Implementation
- Write the minimum code to make the failing test pass.
- Include docstrings on all new public functions, classes, and methods.
- Run the test again. **It must pass.**
- Run the full test suite to verify nothing else broke.

### 3. Refactor — Clean Up
- Improve code quality: rename variables, extract functions, remove duplication.
- Ensure all docstrings are accurate after refactoring — update any that no longer match behavior.
- Run all tests after every change. **Tests must stay green.**
- Only refactor if there's a clear improvement — don't refactor for its own sake.

## Rules
- Never write implementation code before the test exists.
- If fixing a bug, first write a test that reproduces the bug (fails), then fix it (passes).
- One test-implement cycle at a time. Don't batch multiple features.
- If a test is hard to write, that's a signal the interface design needs rethinking.
- Every new function, class, and module must have documentation before the cycle is complete.

## Commands
```bash
# Python
pytest tests/unit -x -v          # Run unit tests, stop on first failure
pytest -x -k "test_name"         # Run specific test

# TypeScript
npx vitest run --reporter=verbose  # Run all tests
npx vitest run path/to/test.ts     # Run specific test file
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nbosiacki) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
