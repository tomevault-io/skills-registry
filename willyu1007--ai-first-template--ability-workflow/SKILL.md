---
name: ability-workflow
description: Ability development workflow from design to registration. Keywords: ability, workflow, development. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Ability Workflow

This workflow describes how to add, register, and safely execute abilities in this template.

---

## 1. Purpose & Scope

Use this workflow when you need to:
- Add a new low-level or high-level ability
- Register an ability so it can appear in `ABILITY.md` catalogs
- Execute abilities through the Tool Runner task interface

Out of scope:
- Manual editing of generated `ABILITY.md` indexes
- Implementing the Tool Runner itself (later phases)

---

## 2. Inputs & Preconditions

Inputs:
- Ability identifier (stable)
- Ability tier/type (low-level vs high-level)
- Intended `entrypoints` (which ABILITY.md catalogs should surface the ability)
- Implementation location (module source vs system utility)

Preconditions:
- Read applicable strategy docs:
  - `/AGENTS.md`
  - `/.system/registry/AGENTS.md`
  - Target `ABILITY.md` catalog (for usage, not for manual edits inside marker blocks)

---

## 3. Step-by-step Flow

### Step 0: Decide ability type and ownership

- Low-level abilities: small, side-effecting operations (script/api/mcp).
- High-level abilities: orchestrated workflows/agents that compose lower-level operations.

Choose the smallest surface area that solves the task.

### Step 1: Implement the ability

- Place implementation in the appropriate area:
  - Module-local abilities under `/modules/<module_id>/src/abilities/`
  - System utilities under `/scripts/` (if appropriate for the template)

### Step 2: Register the ability (SSOT)

- Add a registry entry under `/.system/registry/`:
  - `/.system/registry/low-level/` for low-level abilities
  - `/.system/registry/high-level/` for high-level abilities
- Ensure the entry includes correct `entrypoints` so it appears in the desired `ABILITY.md` catalogs.

### Step 3: Regenerate `ABILITY.md` indexes (tool-owned)

- Do not manually edit the `<!-- BEGIN ABILITY_INDEX -->` marker block.
- Use the registry tooling (Phase 4+) to regenerate catalogs from registries.

### Step 4: Execute via Tool Runner

Canonical execution path:
- `task.create` (preflight + availability)
- `task.run` (guarded execution)

Record results in workdocs.

---

## 4. Outputs & Side Effects

Typical outputs:
- New/updated implementation code
- New/updated registry YAML under `/.system/registry/`
- (When tooling exists) updated `ABILITY.md` indexes for relevant catalogs

---

## 5. Safety Notes

- Abilities are the primary side-effect gateway: treat changes as safety-sensitive.
- Ensure `entrypoints` are minimal (avoid over-exposing powerful abilities).
- Never bypass preflight/guard hooks for convenience.

---

## 6. Related

- `/.system/skills/ssot/repo/architecture-core-mechanisms/runtime-model/SKILL.md`
- `/.system/guides/DOCS_CONVENTION.md`
- `/modules/integration/ABILITY.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
