---
name: tdd
description: > Use when this capability is needed.
metadata:
  author: t2hnd
---

# TDD Skill

Autonomous test-driven development. Write comprehensive tests FIRST, then implement until all tests pass. Prevents "Buggy First-Pass Implementations".

## Workflow: Red → Green → Refactor

### Phase 1: Understanding (READ)

1. **Read existing codebase** to understand:
   - Current architecture and patterns
   - Existing types and interfaces
   - Testing patterns already in use

2. **Identify edge cases** that commonly cause bugs:
   - Empty data / null values
   - Boundary conditions (0, negative, max values)
   - Sorting bias (alphabetical, temporal)
   - Normalization edge cases (division by zero, all same values)
   - Missing configuration (.env variables)
   - Type mismatches (string vs number, undefined fields)

### Phase 2: Test Design (RED)

**BEFORE writing ANY implementation code:**

3. **Write comprehensive tests** covering:
   - Happy path (expected usage)
   - Edge cases (empty, null, boundary)
   - Error conditions (invalid input, missing config)
   - Integration points (API calls, data flow)

4. **Run tests** — they should ALL FAIL:
   ```bash
   npx vitest run <test-file>
   # or
   pytest <test-file> -v
   ```

5. **Verify tests fail for the right reasons:**
   - ✓ Function not implemented yet → correct
   - ✗ Test itself is broken → fix the test

### Phase 3: Implementation (GREEN)

6. **Implement the feature** — minimal code to make tests pass

7. **Run tests iteratively** after each change until ALL pass

8. **Verify no regressions** — run full test suite:
   ```bash
   npx vitest run
   ```

### Phase 4: Verify

9. **Build verification:**
   ```bash
   npm run build
   npm run typecheck
   ```

### Phase 5: Report

10. **Provide summary:**
    ```
    ✓ RED: Wrote 8 tests (edge cases, normalization, sorting bias)
    ✓ GREEN: All 8 tests passing
    ✓ Build passed, no regressions
    Files: src/utils/score.ts (+45), test/score.test.ts (+85, new)
    ```

## Common Edge Cases to Always Test

**Scoring/Ranking:** empty dataset, all same values, single item, division by zero
**Field References:** wrong field name, undefined/null, type mismatches
**Sorting:** alphabetical bias, temporal bias, tie-breaking
**Configuration:** missing .env, dotenv not loaded, API keys unset

## Important Rules

- **NEVER write implementation before tests** — core TDD principle
- **NEVER skip edge case tests** — these catch the most common bugs
- **NEVER ask clarifying questions** — make assumptions and document them
- **DO iterate until 100% pass** — partial success is not done
- **DO run full test suite** — catch regressions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t2hnd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
