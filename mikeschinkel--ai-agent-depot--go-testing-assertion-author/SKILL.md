---
name: go-testing-assertion-author
description: Write and refactor Go tests using Mike's testing best practices and house testing libraries (go-testutil, go-jsontest, go-fsfix, go-pipefunc). Use when authoring assertions, fixtures, golden tests, or test helpers. Use when this capability is needed.
metadata:
  author: mikeschinkel
---

# Testing Assertion Author

Use this skill when the user asks to:
- write tests
- improve assertions/diagnostics
- create fixtures/golden files
- structure table tests
- refactor brittle tests into stable patterns

## Mandatory references (read in order)
1) `references/testing.md`
2) `references/nonnegotiables.md` (still applies: no ignored errors, no compound init `if`)
3) `references/testutil.md`
4) `references/jsontest.md`
5) `references/fsfix.md`
6) `references/pipefunc.md`

## Rules for tests
- **Do not use ClearPath patterns in tests** (no `goto end`, no single-return requirements).
- Optimize for clarity and diagnostic output.
- Prefer declarative tests with minimal indirection.
- When appropriate, use fixtures and golden data for readability.

## House library usage
- If a house library exists for the need, prefer it over ad-hoc helpers.
- When introducing a helper, place it near the tests and keep it small; avoid “framework building”.

## Deliverables
When asked to add or refactor tests:
- Provide complete test code that compiles.
- Include any fixture files needed (describe where to place them).
- For complex comparisons, include failure messages that point to the exact mismatch.

## Assertion checklist (before final output)
- Failure output is actionable (expected vs got, context, and location).
- Test does not depend on wall-clock time, network, or machine-specific paths unless explicitly intended.
- Table tests have clear names and minimal duplication.
- No ignored errors; no compound init-if.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikeschinkel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
