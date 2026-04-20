---
name: verify
description: Run full verification pipeline (types, lint, tests) Use when this capability is needed.
metadata:
  author: harrytran998
---

Run the full verification pipeline for the Sword Art Online project.

## Pipeline

Execute each stage sequentially. Continue through all stages even if one fails.

### Stage 1: TypeScript Type Check
Run `bun tsc --noEmit` and capture the output.
- PASS: exit code 0
- FAIL: report the first 10 errors with file paths

### Stage 2: Lint
Run `oxlint .` and capture the output.
- PASS: no errors (warnings are OK)
- FAIL: report error count and first 5 errors

### Stage 3: Tests
Run `bun test` and capture the output.
- PASS: all tests pass
- FAIL: report failing test names and error messages

## Output

Present a summary table:

| Stage | Status | Details |
|-------|--------|---------|
| Types | PASS/FAIL | Error count or "clean" |
| Lint  | PASS/FAIL | Error/warning count |
| Tests | PASS/FAIL | X passed, Y failed |

**Overall: PASS/FAIL**

If any stage failed, list actionable next steps.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/harrytran998) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
