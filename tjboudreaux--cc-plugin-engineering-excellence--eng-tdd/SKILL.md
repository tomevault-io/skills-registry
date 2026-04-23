---
name: eng-tdd
description: Enforces disciplined RED-GREEN-REFACTOR cycle—write failing test first, watch it fail, write minimal code to pass, then refactor. Use when this capability is needed.
metadata:
  author: tjboudreaux
---

# Test-Driven Development (TDD)

## Overview
Write the test first. Watch it fail. Implement just enough to pass. Refactor while staying green. If you didn’t see the test fail, you don’t know whether it tests the right behavior.

**Iron Law**
```
NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST
```
Wrote code before the test? Delete it. Start from tests.

## When to Use
- Every feature, bug fix, refactor, or behavior change.
- Exceptions only with explicit human approval (throwaway prototypes, generated code, etc.).

## Red-Green-Refactor Cycle
1. **RED** – Write one minimal test describing desired behavior.
   - Clear, specific name.
   - Tests real behavior (avoid unnecessary mocks).
2. **Verify RED** – Run the test; confirm it fails for the expected reason.
3. **GREEN** – Write the simplest code to make the test pass. No extra features.
4. **Verify GREEN** – Run the test suite; ensure the new test and existing tests pass.
5. **REFACTOR** – Clean up code/tests while keeping everything green.
6. Repeat for next behavior.

## Good vs Bad Tests
| Good | Bad |
|------|-----|
| Tests one thing | Vague “does work” |
| Clear name | `test1` |
| Real behavior | Asserts on mocks |

## Why Order Matters
- Tests written after code pass immediately → prove nothing.
- Manual testing isn’t reproducible evidence.
- Keeping prewritten code “for reference” breaks TDD—delete and rebuild from tests.

## Rationalization Countermeasures
| Excuse | Response |
|--------|----------|
| “Too simple” | Simple code breaks—test anyway. |
| “I’ll test later” | Tests-after confirm existing behavior, not requirements. |
| “Manual testing is enough” | No record or rerun; automation is required. |
| “Deleting work is wasteful” | Sunk cost; untested code is debt. |
| “Being pragmatic” | TDD prevents debugging hell; pragmatism = reliability. |

## Verification Checklist
- [ ] Every new behavior guarded by a test.
- [ ] Saw each test fail before implementation.
- [ ] Each failure was due to missing behavior (not typos).
- [ ] Wrote minimal code to make each test pass.
- [ ] Full suite green after changes.
- [ ] Refactors done only while green.
- [ ] Tests cover edge/error cases.

Failed any item? You skipped TDD—start over.

## Integrating Bug Fixes
- Reproduce bug with a failing test first.
- Apply fix to satisfy the test.
- Keep the test to prevent regressions.

## Final Rule
Production code exists only alongside a test that failed before the code was written. Otherwise, redo it with TDD.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tjboudreaux) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
