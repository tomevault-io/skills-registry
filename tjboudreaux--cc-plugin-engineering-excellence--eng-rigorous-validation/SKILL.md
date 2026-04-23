---
name: eng-rigorous-validation
description: Enforce reproducible testing, automated verification, and evidence-driven sign-off before merging any change. Use when this capability is needed.
metadata:
  author: tjboudreaux
---

# Rigorous Validation

## Intent
- Ship only when behavior is proven, not assumed.
- Treat tests, QA scripts, linters, and on-chain/off-chain simulations as first-class deliverables.
- Capture evidence so reviewers can verify quickly.

## Inputs
1. Canonical test commands (unit, integration, e2e, contract sims, UI snapshots).
2. Acceptance criteria plus measurable signals (logs, screenshots, transaction hashes).
3. Migrations/seed data steps required to exercise the change locally.

## Workflow
1. **Design tests before coding**
   - Specify failing cases and target assertions for each requirement.
   - Align on how to stub external services, wallets, or platform APIs.
2. **Automate and isolate**
   - Prefer deterministic, headless test harnesses; avoid manual-only steps.
   - Seed data/fixtures close to tests to prevent global coupling.
3. **Run the full relevant matrix**
   - Cover affected platforms (iOS/Android/web), runtimes, or chain environments.
   - Capture artifacts (logs, screenshots, traces) for any non-deterministic checks.
4. **Track outcomes**
   - Record exact commands run and their commit hash in the PR or issue.
   - File follow-ups for flaky tests before merging.

## Verification
- All targeted tests green and documented; no TODOs or skipped suites without owner sign-off.
- Static analysis, type-checking, formatters, and contract analyzers pass.
- Evidence (artifacts, hashes, screenshots) attached or linked for reviewer inspection.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tjboudreaux) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
