---
name: tdd
description: Strict test-driven development workflow for implementing features Use when this capability is needed.
metadata:
  author: cverrier
---

Implement the following feature using strict TDD: $ARGUMENTS

Think about how to break down the feature into small, testable units (if applicable). Then, for each unit, follow the strict TDD workflow below:
1. Write behavior-oriented tests based on expected input/output pairs - test WHAT the code does, not HOW it does it
2. Do NOT create mock implementations, unless strictly necessary - write real tests that will fail
3. Run `uv run pytest -v` only on the new tests to confirm they fail
4. Write code that passes the tests
5. Do NOT modify the tests during implementation, unless strictly necessary - tests should verify behavior, not implementation details
6. Run `uv run pytest -q --tb=short` to verify tests pass (quiet mode, concise failures). If many tests fail, use `-x` to stop on first failure and fix incrementally
7. Commit the implementation

Once all units are complete and passing, use `gh pr create` to open a PR with test and implementation commits.

IMPORTANT: Tests first, implementation second. Never modify tests during step 6. Tests should verify behavior (inputs/outputs), not implementation details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cverrier) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
