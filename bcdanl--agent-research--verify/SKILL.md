---
name: verify
description: Use at the end of any task that modified code, configs, or outputs. Runs the verification checklist and summarizes pass/fail. Use when this capability is needed.
metadata:
  author: bcdanl
---

## Steps
1) Run: `bash scripts/verify.sh <CONFIG>`
2) If it fails, show the failure point and log paths.
3) If it passes, report:
   - git status
   - artifact paths created/updated
   - verification log path

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bcdanl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
