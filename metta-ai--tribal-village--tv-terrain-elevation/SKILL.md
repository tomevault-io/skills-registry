---
name: tv-terrain-elevation
description: Investigate cliffs, ramps, and elevation traversal. Use when this capability is needed.
metadata:
  author: metta-ai
---

# TV Terrain Elevation

## Workflow
- Run:
- `rg -n "applyBiomeElevation|applyCliffRamps|applyCliffs" src/spawn.nim`
- `sed -n '120,260p' src/spawn.nim`
- `rg -n "canTraverseElevation" src/environment.nim`
- `sed -n '400,460p' src/environment.nim`
- `rg -n "RampUp|RampDown|Cliff" src/terrain.nim src/types.nim src/registry.nim`
- Explain how elevation traversal is handled.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/metta-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
