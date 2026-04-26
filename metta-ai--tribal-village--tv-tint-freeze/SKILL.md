---
name: tv-tint-freeze
description: Investigate tint layers and frozen tiles. Use when this capability is needed.
metadata:
  author: metta-ai
---

# TV Tint Freeze

## Workflow
- Run:
- `rg -n "Tint|tint|Clippy|frozen" src/tint.nim src/colors.nim src/step.nim src/renderer.nim`
- `sed -n '1,200p' src/tint.nim`
- `sed -n '1,160p' src/colors.nim`
- `rg -n "TintLayer" src/environment.nim`
- Summarize tint and freeze mechanics.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/metta-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
