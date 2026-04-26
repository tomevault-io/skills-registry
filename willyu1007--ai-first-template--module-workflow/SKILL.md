---
name: module-workflow
description: Module development workflow from scaffold to integration. Keywords: module, workflow, development. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Module Workflow

This workflow describes the canonical way to create or modify modules while preserving registries, strategy boundaries, and auditability.

---

## 1. Purpose & Scope

Use this workflow when you need to:
- Create a new module instance under `/modules/<module_id>/`
- Update a module’s `MANIFEST.yaml`, flows, or abilities
- Make cross-module changes that require coordination (via integration scenarios)

Out of scope:
- Editing tool-generated registries by hand
- Production deployments (handled in ops scenarios)

---

## 2. Inputs & Preconditions

Inputs:
- `module_id` (stable identifier)
- Intended module responsibilities and boundaries
- Any dependencies on other modules

Preconditions:
- Read the most local applicable strategies:
  - `/AGENTS.md`
  - `/modules/AGENTS.md`
  - `/modules/<module_id>/AGENTS.md` (if the module exists)
- If work is non-trivial: create or update scenario-local workdocs (module-local or integration-local).

---

## 3. Step-by-step Flow

### Step 0: Choose the correct working scope

- If the work touches multiple modules or shared interfaces: use `/modules/integration/` (scenario + workdocs).
- Otherwise: operate inside the target module directory.

### Step 1: Ensure the module layout and strategy exist

- Create `/modules/<module_id>/` (if new).
- Ensure `/modules/<module_id>/AGENTS.md` exists and defines safe boundaries.
- Ensure `MANIFEST.yaml` exists and has the required fields.

Reference templates:
- `/.system/skills/ssot/repo/scaffolding/new-module/template/layout.md`
- `/.system/skills/ssot/repo/scaffolding/new-module/template/manifest_fields.md`

### Step 2: Implement the change in sources of truth

Examples:
- Update `MANIFEST.yaml` flows/abilities declarations.
- Add implementation files under `src/` (and tests as appropriate).

Rules:
- Keep changes minimal and reviewable.
- Record key decisions and tradeoffs in workdocs.

### Step 3: Update derived registries via tooling (do not hand-edit)

- Identify which derived registries need regeneration (typically under `/modules/overview/`).
- If the regeneration ability/tooling is available: run it and commit the generated diffs.
- If tooling is not available yet: **do not manually edit generated registries**. Record what should be regenerated and why, and defer to the registry tooling phase.

### Step 4: Update skill/ability entrypoints if needed

- If the module introduces new durable guidance: create/update supporting docs in the relevant skill package under `/.system/skills/ssot/**` and ensure the skill entrypoint references them.
- If the module introduces new abilities: follow the ability workflow and register them properly.

Never edit tool-owned marker blocks inside `ABILITY.md` (or generated provider skill wrappers).

### Step 5: Review and record outcome

- Validate the module’s boundaries and manifest correctness.
- Ensure workdocs contain an auditable narrative: what changed, why, and what to regenerate.

---

## 4. Outputs & Side Effects

Typical outputs:
- New or updated module sources of truth (`/modules/<module_id>/MANIFEST.yaml`, code, tests)
- Updated workdocs and/or outcomes (module-local or integration-local)
- (When tooling exists) regenerated derived registries under `/modules/overview/`

---

## 5. Safety Notes

- Cross-module changes require coordination and usually human review.
- Never hand-edit generated registries.
- Keep `AGENTS.md` as strategy only; put reusable explanations in `/.system/skills/ssot/**` supporting docs.

---

## 6. Related

- `/.system/skills/ssot/repo/architecture-core-mechanisms/modular-system/SKILL.md`
- `/.system/skills/ssot/repo/architecture-core-mechanisms/ability-workflow/SKILL.md`
- `/.system/skills/ssot/repo/scaffolding/new-module/template/registration_flow.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
