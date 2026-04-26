---
name: gsd-verify
description: Verify completed work against plan requirements Use when this capability is needed.
metadata:
  author: erp-core-dev
---

# GSD Verify -- Work Verification

Verify completed work meets requirements defined in the plan.

## What To Do

1. **Load verification criteria** from ROADMAP.md for the phase.
2. **Run automated checks**: build, tests, lint, custom verify commands.
3. **Check deliverables**: all files exist, all done criteria met, no regressions.
4. **Generate verification report**:
   ```
   ## Phase N Verification Report
   - Build: PASS
   - Tests: 22/22 PASS
   - Coverage: 87% (threshold: 80%)
   - Files created: 8/8
   - Regressions: NONE
   ```
5. **If verification fails**: Create fix tasks, update STATE.md with failures.

## Arguments
- `<phase-number>`: Verify specific phase (default: latest completed)
- `--all`: Verify all completed phases
- `--strict`: Fail on any warning

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erp-core-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
