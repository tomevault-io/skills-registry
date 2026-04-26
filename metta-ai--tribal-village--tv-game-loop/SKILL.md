---
name: tv-game-loop
description: Open the main step loop and action handlers. Use when this capability is needed.
metadata:
  author: metta-ai
---

# TV Game Loop

## Workflow
- Run:
- `sed -n '1,220p' src/step.nim`
- `rg -n "case verb|attackAction|useAction|buildAction" src/step.nim`
- `sed -n '220,900p' src/step.nim`
- Summarize the step loop and action cases.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/metta-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
