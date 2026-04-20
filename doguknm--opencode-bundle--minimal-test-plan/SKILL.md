---
name: minimal-test-plan
description: Rapid test matrix for a change; picks smallest useful checks Use when this capability is needed.
metadata:
  author: doguknm
---

# Minimal Test Plan

Use to decide the smallest effective tests for a change.

## Steps
1) Identify risk areas
- Data writes, auth, external calls, concurrency, migrations

2) Choose minimal checks
- Unit: logic/edge cases in changed functions
- Integration: real deps (DB/API) on the critical path
- Smoke: user/system flow proving the feature works end-to-end

3) Define commands and data
- List exact commands to run; note fixtures or env needed

4) Run or note blockers
- Run the fastest meaningful subset; note anything skipped and why

5) Report
- Tests run + results; tests skipped + rationale

## Outputs
- Short test matrix: which tests, commands, data, results/omissions

## When to stop and ask
- If change is high-risk but only trivial tests are proposed
- If required fixtures/env are unavailable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/doguknm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
