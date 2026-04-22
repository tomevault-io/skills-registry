---
name: therapist-onboarding-workflow
description: Validate therapist onboarding end-to-end flow. Use when the user mentions onboarding tests, therapist onboarding, or onboarding smoke checks. Use when this capability is needed.
metadata:
  author: jeduardo622
---
# Therapist Onboarding Workflow

## Quick Start

1. Validate runtime config for onboarding.
2. Run onboarding flow tests.
3. Summarize failures and remediation.

## Steps

- Follow `docs/onboarding-runbook.md`.
- Use repo script:
  - `scripts/playwright-therapist-onboarding.ts`
- Capture any config or UI failures in the summary.

## Output

- Pass/fail summary with steps that failed.
- Any missing config or data requirements called out.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeduardo622) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
