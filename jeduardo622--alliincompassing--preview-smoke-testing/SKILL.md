---
name: preview-smoke-testing
description: Run preview build smoke tests and verify runtime config. Use when the user mentions preview smoke, runtime config validation, or pre‑deploy checks. Use when this capability is needed.
metadata:
  author: jeduardo622
---
# Preview Smoke Testing

## Quick Start

1. Build preview artifacts.
2. Validate runtime config.
3. Run smoke checks.

## Steps

- Use repo scripts:
  - `scripts/preview-build.ts`
  - `scripts/run-preview-smoke.ts`
  - `scripts/smoke-preview.ts`
- Reference `docs/PREVIEW_SMOKE.md` for the required checks.

## Output

- Clear pass/fail summary.
- Any config mismatches or smoke failures listed with remediation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeduardo622) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
