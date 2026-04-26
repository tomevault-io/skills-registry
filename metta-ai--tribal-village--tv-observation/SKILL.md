---
name: tv-observation
description: Inspect observation space and inventory encoding. Use when this capability is needed.
metadata:
  author: metta-ai
---

# TV Observation

## Workflow
- Run:
- `rg -n "ObservationName|ObservationLayers|TintLayer" src/types.nim`
- `sed -n '150,260p' src/types.nim`
- `rg -n "updateObservations|updateAgentInventoryObs" src/environment.nim`
- `sed -n '1,200p' src/environment.nim`
- Summarize observation layers and inventory handling.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/metta-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
