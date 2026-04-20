---
name: certification-aware-qa
description: Plan and execute certification-aware QA for TV apps. Use when building cross-platform test matrices and validating focus, Back behavior, auth edge cases, search flows, and upsell navigation for Samsung, LG, Hisense, Xbox, and PS5. Use when this capability is needed.
metadata:
  author: vageez
---

# Certification Aware QA

## Overview
Convert platform certification risk into explicit, repeatable test coverage.
Prioritize regressions that block submission: focus, Back, auth, search, and monetization flows.

## Workflow
1. Build platform matrix by OS version, hardware class, and input device.
2. Define must-pass smoke paths per release candidate.
3. Add edge-case suites for deep links, suspend/resume, and offline behavior.
4. Capture reproducible evidence (steps, build ID, logs, video).
5. Track defects by certification severity and retest status.

## Required Test Domains
- Focus traversal and visible indicator correctness.
- Back/Exit behavior across modal, route, and app boundaries.
- Auth/login/logout/session-expiry recoverability.
- Search keyboard/IME transitions and result behavior.
- Upsell prompt navigability and dismiss/revisit semantics.

## Exit Criteria
- All must-pass paths green on each target platform.
- No unresolved P0/P1 certification blockers.
- Known risks documented with mitigation and owner.

## References
- Use `references/device-test-matrix.md` for execution matrix and pass/fail criteria.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vageez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
