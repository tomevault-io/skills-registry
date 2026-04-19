---
name: test-coverage
description: Analyzes test coverage and identifies gaps. Use when adding features or reviewing code to ensure adequate test coverage exists.
metadata:
  author: mabry1985
---

# Test Coverage Analysis

Analyze the project's test coverage and identify gaps.

## Process:

1. Find all test files using Glob patterns (`**/*.test.*`, `**/*.spec.*`, `**/__tests__/**`)
2. For each source module, check if a corresponding test exists
3. Read test files to understand what's covered
4. Identify modules with no tests or thin test coverage

## Report:

- **Covered** — Modules with good test coverage
- **Gaps** — Modules missing tests entirely
- **Thin** — Modules with tests that don't cover edge cases
- **Suggested tests** — Specific test cases that should be added, prioritized by risk

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mabry1985) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
