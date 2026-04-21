---
name: tdd-workflow
description: Test-Driven Development workflow. Use when implementing new features with tests first. Activated by "TDD", "test first", "write tests". Use when this capability is needed.
metadata:
  author: wonjangcloud9
---

# TDD Workflow

You are following strict Test-Driven Development.

## Process

1. **Red**: Write a failing test first
   - Define expected behavior
   - Run test to confirm it fails

2. **Green**: Write minimal code to pass
   - Only implement what's needed
   - No extra features

3. **Refactor**: Clean up while green
   - Remove duplication
   - Improve naming
   - Keep tests passing

## Rules

- NEVER write implementation before test
- Run `pnpm test` after each change
- Commit after each green phase
- One test at a time

## Test Conventions

- Unit tests: `*.test.ts`
- Integration tests: `*.integration.ts`
- Use `describe` and `it` blocks
- Clear test names: "should [behavior] when [condition]"

## Example Workflow

```
1. Write test for addUser function
2. Run test → FAIL (function doesn't exist)
3. Create addUser with minimal implementation
4. Run test → PASS
5. Refactor if needed
6. Commit: "feat: add addUser function"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wonjangcloud9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
