---
name: test
description: Run tests, analyze failures, and propose fixes Use when this capability is needed.
metadata:
  author: sakebomb
---

Run the test suite, analyze results, and propose fixes for any failures.

## Scope

- `$ARGUMENTS` is empty or "all": run `make test` (full suite, fail-fast by tier).
- `$ARGUMENTS` is "unit": run `make test-unit`.
- `$ARGUMENTS` is "integration": run `make test-integration`.
- `$ARGUMENTS` is "agent": run `make test-agent`.
- `$ARGUMENTS` is a file path: run `make test-file FILE=<path>`.

## Process

1. Run the specified tests.
2. If all pass: report success with a brief summary.
3. If any fail:
   - Parse the error output to identify the root cause.
   - Read the failing test and the code under test.
   - Propose a fix — explain what went wrong and how to resolve it.
   - Do NOT apply the fix automatically — present it for approval.

## Output

Write detailed results to `scratch/test_results_latest.md` with:
- Pass/fail counts per tier
- Failure details (test name, error message, relevant code)
- Proposed fixes if applicable

Return a ≤5 line summary to the main context.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sakebomb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
