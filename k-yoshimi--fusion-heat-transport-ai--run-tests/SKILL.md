---
name: run-tests
description: Run tests and fix failures if any Use when this capability is needed.
metadata:
  author: k-yoshimi
---

# Run Tests

Execute the test suite and fix any failures.

## Steps

1. Run `python -m pytest tests/ -v`
2. If all tests pass, report results and finish
3. If any tests fail:
   - Analyze the error messages of failed tests
   - Read the relevant source code
   - Explain the fix to the user before applying it
   - Re-run tests to confirm the fix
4. Append bug fix details to HISTORY.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/k-yoshimi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
