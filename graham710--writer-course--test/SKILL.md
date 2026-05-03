---
name: test
description: Run the pytest suite and report results Use when this capability is needed.
metadata:
  author: graham710
---

# /test -- Run Project Tests

Run the full test suite and report results.

## Steps
1. Run `pytest tests/ -v --tb=short` from the project root
2. If any tests fail, show the failing test name, the assertion error, and the relevant source file
3. If all tests pass, confirm the count
4. Do NOT modify any files -- this is a read-only diagnostic

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/graham710) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
