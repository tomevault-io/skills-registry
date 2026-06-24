---
name: staging-validation-phase
description: Standard Operating Procedure for staging validation. Manual testing before production. Use when this capability is needed.
metadata:
  author: oxie
---

# Staging Validation Phase: Standard Operating Procedure

> **Training Guide**: Validate staging deployment before promoting to production.

## Phase Overview
**Purpose**: Manual testing on staging, smoke tests, sign-off
**Inputs**: Staging deployment
**Outputs**: Validation report, sign-off decision
**Expected duration**: 30-60 minutes

## Execution Steps
1. Run smoke tests on staging
2. Test critical user flows
3. Verify data migrations
4. Check rollback capability
5. Sign-off decision (approve/reject)

## Common Mistakes
- Validation steps skipped
- Smoke tests insufficient
- Sign-off unclear

## Completion Criteria
- [ ] Smoke tests pass
- [ ] Critical flows verified
- [ ] Sign-off documented

_This SOP guides staging validation._

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oxie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
