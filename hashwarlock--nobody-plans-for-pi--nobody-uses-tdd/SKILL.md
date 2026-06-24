---
name: nobody-uses-tdd
description: Use when implementing any feature or bugfix, before writing implementation code
metadata:
  author: hashwarlock
---

# Test-Driven Development (TDD)

## Overview

Write the test first. Watch it fail. Write minimal code to pass.

**Core principle:** If you didn't watch the test fail, you don't know if it tests the right thing.

**Violating the letter of the rules is violating the spirit of the rules.**

## The Iron Law

```
NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST
```

Write code before the test? Delete it. Start over.

**No exceptions:**
- Don't keep it as "reference"
- Don't "adapt" it while writing tests
- Don't look at it
- Delete means delete

## Red-Green-Refactor

### RED — Write Failing Test

Write one minimal test showing what should happen.

- One behavior per test
- Clear name describing the behavior
- Real code, no mocks unless unavoidable

### Verify RED — Watch It Fail (MANDATORY)

Run the test. Confirm:
- Test fails (not errors)
- Failure message is expected
- Fails because feature is missing (not typos)

**Test passes?** You're testing existing behavior. Fix the test.

### GREEN — Minimal Code

Write the simplest code to pass the test. Nothing more.

Don't add features, refactor other code, or "improve" beyond the test.

### Verify GREEN — Watch It Pass (MANDATORY)

Run the test. Confirm:
- Test passes
- Other tests still pass
- Output is clean (no errors, warnings)

### REFACTOR — Clean Up

After green only. Remove duplication, improve names, extract helpers.

Keep tests green. Don't add behavior.

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "Too simple to test" | Simple code breaks. Test takes 30 seconds. |
| "I'll test after" | Tests passing immediately prove nothing. |
| "Need to explore first" | Fine. Throw away exploration, start with TDD. |
| "Test hard = design unclear" | Listen to the test. Hard to test = hard to use. |
| "TDD will slow me down" | TDD is faster than debugging. |
| "Already manually tested" | Ad-hoc ≠ systematic. No record, can't re-run. |
| "Deleting X hours is wasteful" | Sunk cost fallacy. Keeping unverified code is debt. |

## Red Flags — STOP and Start Over

- Code before test
- Test after implementation
- Test passes immediately
- Can't explain why test failed
- "Just this once"
- "I already manually tested it"
- "This is different because..."

**All of these mean: Delete code. Start over with TDD.**

## Bug Fix Flow

1. **RED** — Write test reproducing the bug
2. **Verify RED** — Watch it fail with the bug symptom
3. **GREEN** — Fix the bug with minimal change
4. **Verify GREEN** — Test passes, all tests pass
5. **Commit**

Never fix bugs without a test.

## Verification Checklist

- [ ] Every new function has a test
- [ ] Watched each test fail before implementing
- [ ] Each test failed for expected reason
- [ ] Wrote minimal code to pass each test
- [ ] All tests pass
- [ ] Output is clean
- [ ] Edge cases and errors covered

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hashwarlock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
