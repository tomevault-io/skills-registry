---
name: design-implementation
description: Guide general software design and implementation choices. Use for feature work, refactors, or architecture decisions across this repo. Use when this capability is needed.
metadata:
  author: robinbreast
---

Use this skill when shaping solutions that span multiple files or modules.

Shared conventions
- Follow [`AGENTS.md`](../../../AGENTS.md) for coding style, TypeScript/lint rules, and testing conventions.
- Keep this skill focused on design and implementation tradeoffs.

Principles
- Follow [`AGENTS.md`](../../../AGENTS.md) for design-first, SOLID/DRY, and minimal-change guidance.
- Design before implementation: confirm architecture choices before editing files.
- Prefer small, focused changes over broad refactors.
- Align with existing patterns and TypeScript strictness.

Design checklist
1. Identify the smallest change that meets requirements.
2. Keep responsibilities narrow and testable.
3. Avoid cross-module coupling unless required.
4. Prefer explicit types over inference for exported APIs.
5. Update user-facing docs when behavior changes.

Implementation checklist
1. Read the target files and match their style.
2. Avoid introducing new dependencies unless necessary.
3. Handle errors explicitly and surface user-visible errors via VS Code UI.
4. Add or update tests when behavior changes.
5. Keep code and docs synchronized.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/robinbreast) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
