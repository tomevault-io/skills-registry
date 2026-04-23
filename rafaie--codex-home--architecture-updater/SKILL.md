---
name: architecture-updater
description: Keep high-level architecture specs/diagrams aligned with the evolving codebase. Use when this capability is needed.
metadata:
  author: rafaie
---

1) Read `spec/architecture.md` (if present), `spec/index.md`, and recent ADRs under `spec/decisions/`.
2) Update/create `spec/architecture.md` with:
   - Components (bulleted)
   - Data flow (steps)
   - External dependencies
   - Key interfaces/contracts (links)
   - “What changed recently” (short changelog)
3) If useful, add/update a diagram under `spec/diagrams/` (e.g., Mermaid).
4) Ensure `spec/index.md` links to architecture and the latest ADRs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rafaie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
