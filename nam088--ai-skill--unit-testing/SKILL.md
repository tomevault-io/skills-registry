---
name: unit-testing
description: Guidelines for writing and running unit tests using Bun's built-in test runner. Use when the user asks to write tests, add coverage, or fix failing tests. Use when this capability is needed.
metadata:
  author: nam088
---

# Unit Testing Skill

## When to use
- When the user asks to "write tests", "add unit tests", "test this function".
- When creating new modules that require verification.

## Instructions

### 1. Test Runner
- Use **Bun's built-in test runner** (`bun test`).
- Do NOT install Jest or Mocha unless explicitly requested.

### 2. File Conventions
- Place tests in the `test/` directory or alongside source files (e.g., `src/foo.test.ts`) depending on project structure.
- Naming convention: `*.test.ts` or `*.spec.ts`.

### 3. Syntax & Imports
- Import test utilities from `bun:test`:
  ```typescript
  import { describe, it, expect, beforeEach, afterEach } from 'bun:test';
  ```
- Use `describe` blocks to group tests by function or module.
- Use `it` or `test` for individual test cases.

### 4. Mocking
- Use `mock` from `bun:test` for function mocking.
  ```typescript
  import { mock } from 'bun:test';
  const myMock = mock(() => 'value');
  ```
- Reset mocks in `afterEach` if necessary.

### 5. Execution
- Run all tests: `bun test`
- Run specific file: `bun test path/to/file.test.ts`
- Run/Watch mode: `bun test --watch`

### 6. Best Practices
- **Isolation**: Tests should not depend on each other.
- **Clarity**: Test names should describe the expected behavior (e.g., `it('should return 404 when user not found')`).
- **Coverage**: Aim to test both success and failure paths.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nam088) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
