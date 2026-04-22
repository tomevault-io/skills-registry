---
name: mutation-test
description: Run mutation testing to evaluate test suite quality. Use when user says "mutation test", "test quality", "are my tests strong enough", or "find weak tests". Introduces code mutations and reports mutation score with suggestions for improvement. Use when this capability is needed.
metadata:
  author: gr1m0h
---

Run mutation testing to evaluate test suite quality.

## Context

Test suite status:
!`npm test -- --passWithNoTests 2>&1 | tail -5 || pytest --co -q 2>&1 | tail -5 || go test ./... 2>&1 | tail -5 || echo "Could not detect test runner"`

## Target: $ARGUMENTS

## Instructions

1. Verify baseline test suite passes
2. Configure mutation testing tool for the project language
3. Run mutation testing on target directory
4. Analyze survived mutants
5. Report mutation score and identify weak test areas
6. Suggest additional tests to kill survived mutants

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gr1m0h) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
