---
name: superpowers-debug
description: Systematic debugging: reproduce, isolate, form hypotheses, instrument, fix, and add regression tests. Use when troubleshooting errors, failing tests, or unexpected behavior. Use when this capability is needed.
metadata:
  author: anthonylee991
---

# Debug Skill

## When to use this skill
- runtime errors, flaky tests, wrong outputs
- “it used to work” regressions
- performance or timeout problems (initial triage)

## Debug workflow (do not skip steps)
1. **Reproduce**
   - Capture exact error, inputs, environment, command.
2. **Minimize**
   - Reduce to smallest repro (one file, one function, smallest dataset).
3. **Hypotheses (2–5)**
   - Rank by likelihood.
4. **Instrument**
   - Add temporary logging/assertions or use existing diagnostics.
5. **Fix**
   - Smallest change that removes root cause.
6. **Prevent**
   - Add regression test or permanent guard/validation.
7. **Verify**
   - Run the failing case + relevant suites.

## Reporting format
- Symptom
- Repro steps
- Root cause
- Fix
- Regression protection
- Verification

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anthonylee991) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
