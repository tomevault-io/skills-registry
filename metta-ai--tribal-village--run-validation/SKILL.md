---
name: run-validation
description: Run the standard local validation steps for tribal-village (alias: tv-validate). Use when this capability is needed.
metadata:
  author: metta-ai
---

# Run Validation

## Workflow
- Run:
- `nim c -d:release tribal_village.nim`
- `timeout 15s nim r -d:release tribal_village.nim` (or `gtimeout` on macOS)
- `nim r --path:src tests/ai_harness.nim`
- Summarize results and failures.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/metta-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
