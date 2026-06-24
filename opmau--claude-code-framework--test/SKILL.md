---
name: test
description: Run tests. Use when the user says "test", "run tests", or before commits. Use when this capability is needed.
metadata:
  author: opmau
---

# /test — Run project tests

Run the test suite and report results.

## Steps

1. If `$ARGUMENTS` is provided, run the specific test:
   ```
   [YOUR SPECIFIC TEST COMMAND] $ARGUMENTS
   ```

2. If no arguments, run the full test suite:
   ```
   [YOUR FULL TEST COMMAND HERE]
   ```

3. Parse the output and report in this format:
   ```
   TESTS: [PASS/FAIL]
   Passed: [count]
   Failed: [count]
   Skipped: [count]

   [If failures:]
   FAILED: [test name] — [error summary]
   FAILED: [test name] — [error summary]
   ```

4. If tests fail, identify which test failed and the likely cause. Do NOT attempt to fix — just report.

## Notes

- Run the FULL suite before commits (no arguments)
- If a specific module was just changed, suggest which specific tests to run based on the test mapping in CLAUDE.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/opmau) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
