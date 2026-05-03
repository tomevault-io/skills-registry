---
name: test-and-lint
description: Run tests (non-watch mode) and lints, fix any errors, and verify fixes pass Use when this capability is needed.
metadata:
  author: jeffbalagosa
---

# Test and Lint

Run the full test and lint suite, fix any failures, and verify the fixes.

## Execution Steps

### 1. Run Tests (Non-Watch Mode)

```bash
npm run test:run
```

This runs Vitest in non-watch mode. Capture all output including:
- Failed test names and locations
- Error messages and stack traces
- Assertion failures

### 2. Run Lints

```bash
npm run lint
```

Capture all ESLint output including:
- File paths with errors/warnings
- Rule violations
- Line numbers

### 3. Analyze Failures

For each failure:
- Identify the file and line number
- Understand the root cause (not just the symptom)
- Determine if it's a test issue or code issue

### 4. Fix Errors

**Priority order:**
1. TypeScript/compilation errors (blocks everything)
2. Test failures (functionality broken)
3. Lint errors (code quality)
4. Lint warnings (optional, fix if simple)

**For test failures:**
- Read the failing test to understand expected behavior
- Read the implementation being tested
- Fix the code OR the test (whichever is wrong)
- Prefer fixing code over weakening tests

**For lint errors:**
- Apply the fix that satisfies the rule
- Don't disable rules without good reason
- Use `// eslint-disable-next-line` only as last resort

### 5. Verify Fixes

After making fixes, run both commands again:

```bash
npm run test:run && npm run lint
```

**Repeat steps 3-5** until all tests pass and no lint errors remain.

### 6. Report Results

Provide a summary:
- What failed initially
- What was fixed and how
- Final verification status (pass/fail)

## Quick Reference

| Command | Purpose |
|---------|---------|
| `npm run test:run` | Run Vitest (non-watch) |
| `npm run lint` | Run ESLint |
| `npm run build` | TypeScript + Vite build |

## Common Issues

**Test timeouts:** Check for missing `await` on async operations

**Import errors:** Verify file paths and exports match

**Type errors:** Run `npm run build` to get full TypeScript diagnostics

**React testing errors:** Ensure proper cleanup with `@testing-library/react`

## Do NOT

- Disable tests to make them pass
- Weaken assertions to avoid failures
- Add `eslint-disable` comments without explanation
- Skip the verification step
- Leave partial fixes (either fully fix or revert)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeffbalagosa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
