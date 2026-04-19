---
name: compound-v-debug
description: Systematic debugging — reproduce, isolate, form hypotheses, instrument, fix, and add regression tests. Use when troubleshooting errors, failing tests, or unexpected behavior. Use when this capability is needed.
metadata:
  author: shermanhuman
---

# Debug Skill

**Announce at start:** "Debugging: [symptom]. Following systematic debug workflow."

## When to use this skill

- runtime errors, flaky tests, wrong outputs
- "it used to work" regressions
- performance or timeout problems (initial triage)

## Debug workflow (do not skip steps)

1. **Reproduce**
   - Capture exact error, inputs, environment, command.
2. **Research** (parallel — invoke multiple tool calls in the same response)
   - Search the web for the exact error message or symptom.
   - Search the web for known issues in the relevant library/framework version (use `stack.md` versions).
   - Read related source files in parallel.
3. **Minimize**
   - Reduce to smallest repro (one file, one function, smallest dataset).
4. **Hypotheses (2–5)**
   - Rank by likelihood.
   - Investigate independent hypotheses in parallel where possible (e.g., check config + check logs + check deps simultaneously).
5. **Instrument**
   - Add temporary logging/assertions or use existing diagnostics.
6. **Fix**
   - Smallest change that removes root cause.
7. **Prevent**
   - Add regression test or permanent guard/validation.
8. **Verify**
   - Run the failing case + relevant suites.

## Reporting format

- Symptom
- Repro steps
- Root cause
- Fix
- Regression protection
- Verification

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shermanhuman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
