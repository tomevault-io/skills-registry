---
name: debug-repro-fix-regression-test
description: Use when debugging from logs/symptoms to reproduce, isolate, patch, and add a regression test.
metadata:
  author: juantaco4you
---

## Goal
Turn an incident into a reproducible test case and a minimal, correct fix.

## Workflow
1. Clarify the symptom:
   - Expected vs actual
   - Frequency and scope
   - Environment details
2. Reproduce:
   - Local repro steps if possible
   - Otherwise create a deterministic harness/test
3. Isolate:
   - Identify the failing boundary
   - Reduce to smallest input that triggers the issue
4. Root cause:
   - Code path analysis
   - State and timing considerations
   - Validate with targeted logging or debugger
5. Fix:
   - Minimal patch
   - Defensive checks only if they are correct and documented
6. Regression test:
   - Add a test that fails without the fix and passes with it
7. Verify:
   - Run relevant test suites
   - Consider performance impact

## Output format
- Repro steps or repro test
- Root cause
- Fix summary
- Regression test description
- Verification commands

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/juantaco4you) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
