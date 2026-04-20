---
name: debugger
description: Systematic debugging skill for investigating and resolving issues in existing code. Use when this capability is needed.
metadata:
  author: spenceriam
---

# Debugger Skill

This skill provides a systematic approach to debugging issues in new or existing codebases.

## What it is

A structured methodology for finding and fixing bugs rather than applying random trial-and-error changes.

## What it does

- Guides reproduction of issues.
- Implements systematic root cause analysis.
- Encourages minimal, well-understood fixes.
- Ensures fixes are verified and do not introduce regressions.
- Captures findings so they can inform future work.

## Why it is important

Undisciplined debugging is inefficient and risks masking symptoms without addressing root causes. A systematic approach improves understanding of the codebase and leads to durable fixes that are easier to maintain.

## Process

1. **Reproduce**
   - Confirm that the bug exists and can be reproduced reliably.
   - Capture exact steps, inputs, and environment details.

2. **Isolate**
   - Narrow down the code paths where the bug manifests.
   - Use logging, breakpoints, or targeted tests to localize the problem.

3. **Hypothesize**
   - Form one or more theories about the root cause.
   - Prefer small, testable hypotheses over broad assumptions.

4. **Test**
   - Design experiments or tests to confirm or refute each hypothesis.
   - Update your understanding based on results.

5. **Fix**
   - Implement the minimal change that addresses the confirmed root cause.
   - Avoid opportunistic refactors unless they are essential to the fix and approved.

6. **Verify**
   - Re-run reproduction steps to ensure the bug is resolved.
   - Run existing tests and add new ones if coverage is lacking.
   - Check for likely regressions in adjacent behavior.

7. **Document**
   - Record what caused the bug and why the fix works.
   - Note any follow-up work or areas that may need deeper refactor.

This skill should be combined with the bugfix workflow and code-review skill for a complete debugging lifecycle.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spenceriam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
