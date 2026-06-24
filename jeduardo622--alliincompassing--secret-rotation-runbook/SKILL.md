---
name: secret-rotation-runbook
description: Rotate and validate secrets following the project runbook. Use when the user mentions secret rotation, API key changes, or credential updates. Use when this capability is needed.
metadata:
  author: jeduardo622
---
# Secret Rotation Runbook

## Quick Start

1. Follow the rotation runbook for the target system.
2. Validate secrets with checks.
3. Document updated keys without exposing values.

## Steps

- Follow `docs/SECRET_ROTATION_RUNBOOK.md`.
- Use repo script:
  - `scripts/check-secrets.ts`
- Never log or commit secrets; mask as `****`.

## Output

- Validation status and any missing secrets.
- Clear next steps if rotation fails.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeduardo622) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
