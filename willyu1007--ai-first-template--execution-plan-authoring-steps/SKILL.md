---
name: execution-plan-authoring-steps
description: Step-by-step plan authoring process. Keywords: plan, steps, authoring. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Execution Plan Authoring Steps

This doc contains the step-by-step workflow. For scope and inputs, see
`/.system/skills/ssot/repo/execution-plans/execution-plan-authoring/SKILL.md`.

---

## Steps

1. **Confirm you need a full execution plan (complexity triage)**
   - If the task is non-trivial (criteria in the entrypoint), proceed with this workflow.
   - If the task is trivial, write a micro plan and stop here.

2. **Set up workdocs (durable task state)**
   - Create or open a workdocs folder for this task.
   - Ensure these files exist:
     - `plan.md`: the execution plan (YAML header + narrative)
     - `context.md`: constraints, assumptions, pointers, gates
     - `tasks.md`: granular checklists, including approvals

3. **Write `context.md` first (constraints before steps)**
   - Record:
     - Scope boundaries (in-scope / out-of-scope)
     - Applicable strategy docs (`AGENTS.md`) and authoritative SSOT pointers
     - Forbidden areas and stop-the-line conditions
     - Required human inputs or approvals and why

4. **Draft `plan.md` YAML header (machine-readable plan)**

   `plan.md` MUST start with a YAML header in this shape:

   ```yaml
   ---
   name: <short plan name>
   overview: <1-2 lines describing success + non-goals if needed>
   todos:
     - id: <kebab-case-todo-id>
       content: <verb + object; one deliverable>
       status: pending | in_progress | completed

       # required: every todo must be executable + verifiable
       outputs:
         - /modules/<module_id>/src/...
       checks:
         - "cmd: <command to run>"
         - "manual: <explicit verification statement>"

       # optional
       dependencies:
         - <todo-id>
       gates:
         - type: human_approval | human_input | policy_check
           reason: <why this gate exists>
           by: <optional who provides it>
       notes: <optional>
   ---
   ```

   Note:
   - This YAML header is for `workdocs/.../plan.md` and is distinct from `SKILL.md` front matter.

   Rules:
   - Exactly one todo SHOULD be `in_progress` at a time.
   - A todo SHOULD NOT move to `in_progress` until its `dependencies` are `completed`.
   - `outputs` MUST be non-empty.
     - At least one `outputs` item MUST be a repo-root absolute path (starts with `/`).
     - Additional non-path outputs MAY be included when they are specific and verifiable.
   - `checks` MUST be non-empty and explicit.
     - Prefer `cmd:` and `manual:` prefixes to keep meaning unambiguous.
   - `gates` SHOULD be used whenever the task requires human checkpoints or policy constraints.

5. **Write the `plan.md` narrative (human-readable plan)**

   `plan.md` MUST also include (at minimum) these sections:
   - `Validation` (how you will verify correctness)
   - `Definition of Done` (acceptance criteria)
   - `Risks & Gates` (risks, mitigations, and which todos are gated)

   Recommendations:
   - Keep the narrative aligned to the todo list (no hidden steps).
   - Avoid vague steps; each step should point to outputs and checks.

6. **Expand `tasks.md` (granular execution checklist)**
   - For each todo, add a checklist of sub-tasks and checkpoints.
   - Include:
     - `todo_id` linkage
     - approval tasks (who/what/when)
     - stop-the-line conditions

7. **Run a plan review pass and iterate**
   - Use the plan review workflow to find gaps before execution:
   - Update:
     - missing outputs/checks
     - missing gates
     - unclear dependencies
     - missing validation/DoD items

---

## Outputs

- A workdocs folder containing:
  - `plan.md` with a YAML todo header (outputs/checks per todo) and a narrative (Validation/DoD/Risks & Gates)
  - `context.md` capturing constraints, assumptions, and approvals
  - `tasks.md` capturing granular checklists and gates

---

## Safety Notes

- Treat gates as authoritative: do not execute gated steps until the gate is resolved.
- Prefer conservative, explicit checks; avoid "trust me" validation.
- If the plan touches tool-owned regions (ABILITY indexes, generated skill wrappers), plan to regenerate via tooling; do not hand-edit tool-owned content.

---

## Related

- `/.system/skills/ssot/repo/architecture-core-mechanisms/knowledge-metadata/SKILL.md`
- `/.system/skills/ssot/repo/architecture-core-mechanisms/module-workflow/SKILL.md`
- `/.system/skills/ssot/repo/architecture-core-mechanisms/ability-workflow/SKILL.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
