---
name: spec-test-driven-development
description: Use when user requests new features or functionality. Defines complete workflow from specification through testing to implementation.
metadata:
  author: craigtkhill
---

# Spec-TDD Development Workflow

When developing ANY new feature or functionality, follow this strict workflow:

## Workflow Steps

### 0. Update Dependencies FIRST
**Use the `update-dependencies` skill to update all dependencies before starting any new feature.**

- Run before writing the spec for any new feature cycle
- Ensures you build on up-to-date, secure dependencies
- If updates cause test failures, resolve them before proceeding

### 1. Write Specification FIRST (spec.yaml)
**Use the `specifying-requirements` skill to write specifications following project conventions.**

The specifying-requirements skill provides detailed guidelines for:
- Feature user story format (as_a, i_want, solutions)
- Requirements organization and naming (REQ-XXX-NNN)
- Requirement writing guidelines (atomic, testable, present tense)
- Scenario writing (given/when/then format)
- Proper YAML spec structure

After writing spec:
- Get user approval on spec before proceeding to tests

### 2. Write Tests SECOND (before implementation)
- Write tests based on the spec requirements
- One test per assertion (performance costs permitting)
- Test naming: `test_should_{expected_behavior}`
- Test files: `tests/test_{feature}/test_{module}.rs`

**When to write unit tests vs acceptance tests:**
- **Unit tests**: For isolated logic that doesn't require mocking
- **Acceptance tests**: Use the `acceptance-test` skill for guidance on when and how to write acceptance tests

### 3. Implement Code LAST
- Write minimal code to make tests pass
- Follow patterns from spec
- Reuse existing infrastructure where possible
- Update todo list as you work
- **CRITICAL: Update spec.yaml requirement markers as you complete each requirement**
  - After writing test: Set `test` field to `unit` or `acceptance`
  - After implementing code: Set `code` field to `done`
  - Example: `test: to-implement, code: to-implement` → `test: unit, code: to-implement` (test written) → `test: unit, code: done` (code implemented)

### 4. Run Tests to Verify Implementation
- **CRITICAL: After completing implementation, ALWAYS run the tests**
- All tests must pass before marking work as complete
- If tests fail, fix the implementation and re-run until all tests pass
- **DO NOT claim work is complete without running and passing tests**

**Use the `test-driven-development` skill for language-specific test running instructions:**
- The TDD skill contains detailed patterns for running tests
- For other languages: See corresponding files in `test-driven-development/{language}.md`

### 5. Run Pre-commit Hooks Before Completion
- **CRITICAL: After tests pass, ALWAYS run pre-commit hooks**
- Pre-commit hooks run linters, formatters, and static analysis tools
- Fix any issues raised by pre-commit hooks before marking work as complete
- **DO NOT claim work is complete without running and passing pre-commit hooks**

**Run pre-commit hooks:**
```bash
pre-commit run --all-files
```

## Commit Workflow

**Use the `write-commit-message` skill for git commit guidelines.**

## Do NOT Proceed Without

1. ❌ Do NOT write implementation code before updating dependencies
2. ❌ Do NOT write implementation code before spec
3. ❌ Do NOT write implementation code before tests
4. ❌ Do NOT skip writing tests
5. ❌ Do NOT write multiple assertions per test (unless justified)
6. ❌ Do NOT skip running tests after implementation
7. ❌ Do NOT skip running pre-commit hooks before completion
8. ✅ DO update dependencies → write spec → tests → implementation → run tests → run pre-commit in that order
9. ✅ DO get user approval on spec before proceeding
10. ✅ DO use TodoWrite to track progress
11. ✅ DO run tests and verify all pass
12. ✅ DO run pre-commit hooks and fix all issues

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/craigtkhill) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
