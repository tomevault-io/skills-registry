---
name: eng-testing-anti-patterns
description: Use when writing or modifying tests, adding mocks, or considering test-only hooks—prevents common mistakes like testing mocks, polluting production with test helpers, and mocking without understanding dependencies.
metadata:
  author: tjboudreaux
---

# Testing Anti-Patterns

## Overview
Tests must validate real behavior, not mock behavior. Mocks are isolation tools; they are never the subject under test. Follow these guardrails whenever you touch tests or mocks.

**Iron Laws**
```
1. NEVER test mock behavior
2. NEVER add test-only methods to production classes
3. NEVER mock without understanding dependencies
4. NEVER ship incomplete mocks
5. NEVER treat integration tests as an afterthought
```

## Anti-Pattern 1: Testing Mock Behavior
- ❌ Example: asserting `getByTestId('sidebar-mock')` exists after mocking `<Sidebar/>`.
- Why wrong: proves mock works, says nothing about component.
- ✅ Fix: test the real component (or mock only for isolation but assert behavior, not mock internals).

**Gate:** Before asserting on a mock, ask “Am I testing actual behavior or just the mock?” If it’s the mock, delete the assertion or unmock.

## Anti-Pattern 2: Test-Only Methods in Production
- ❌ Example: adding `Session.destroy()` only used in tests.
- Why wrong: pollutes prod API, dangerous if called live.
- ✅ Fix: move cleanup helpers to test utils; production classes expose only real lifecycle.

**Gate:** Before adding a method, ask “Is this only for tests?” and “Does this class own the lifecycle?” If “yes” or “no” respectively, stop.

## Anti-Pattern 3: Mocking Without Understanding
- ❌ Mocking a high-level method that also writes config, causing duplicate detection to fail.
- ✅ Mock only the slow/external portion (e.g., server startup) so necessary side effects remain.

**Gate:** Before mocking, answer:
1. What side effects does the real method have?
2. Does the test rely on them?
3. Do I fully understand the dependency chain?
If unsure, run with real implementation first, observe, then mock minimally.

## Anti-Pattern 4: Incomplete Mocks
- ❌ Mock response with only fields you use; downstream needs `metadata.requestId`.
- ✅ Mirror the full real schema (status, data, metadata, etc.).

**Gate:** Examine real API/docs, include every field consumers might touch. If uncertain, copy the full structure.

## Anti-Pattern 5: Integration Tests as Afterthought
- ❌ Implementation “done” without tests.
- ✅ Follow TDD: write failing test, implement, refactor.

## Over-Complex Mocks
If mock setup eclipses test logic, reconsider. Ask “Do we need a mock here?” Integration tests with real components may be simpler.

## TDD Prevents These
Following TDD ensures tests verify real behavior (fail first), prevents test-only prod code, and reveals when mocks are unnecessary.

## Quick Reference
| Anti-Pattern | Fix |
|--------------|-----|
| Assert on mock | Test real behavior or unmock |
| Test-only methods | Move to test utilities |
| Blind mocking | Understand dependencies first |
| Partial mocks | Match real API schema |
| No tests | TDD cycle |

## Red Flags
- Assertions referencing `*-mock`
- Methods only invoked from tests
- Mock setup >50% of test
- Tests fail when mock removed
- “Mocking to be safe” rationale

**Bottom line:** Mocks isolate but are never the unit under test. Test real behavior, and let TDD guide your workflow.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tjboudreaux) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
