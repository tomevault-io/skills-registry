---
name: tv-economy-respawn
description: Inspect inventory, stockpile, altars, hearts, and respawn logic. Use when this capability is needed.
metadata:
  author: metta-ai
---

# TV Economy Respawn

## Workflow
- Run:
- `rg -n "altar|hearts|respawn" src/step.nim src/items.nim src/types.nim`
- `sed -n '1800,2100p' src/step.nim`
- `sed -n '1,200p' src/items.nim`
- Summarize economy and respawn flow.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/metta-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
