---
name: todo-epic-converter
description: Transition process for promoting pending items in `master_todo.md` to active Epics with stories. Use when the currently active item is finished or when the Product Manager needs to start the next high-level project phase. Use when this capability is needed.
metadata:
  author: horschig
---

# Todo Epic Converter

Transform high-level goals from `master_todo.md` into actionable implementation units.

## Workflow

1. **Evaluate Current State**:
   - Read `./docs/todo/master_todo.md` and check `## CURRENTLY ACTIVE`.
   - **If current item has `[x]`**: Mark it complete in `## EPICS` and find the next `[ ]` item.
   - **If current item has `[ ]` but needs to be paused**: Move its current stories/tasks down and set the next Epic as `CURRENTLY ACTIVE`.

2. **Epic Promotion**:
   - Set the chosen EPIC as `CURRENTLY ACTIVE`.
   - Update its status in the `## EPICS` list to `← ACTIVE`.

3. **User Story Generation**:
   - Create 2-4 User Stories that fulfill the Epic's goal.
   - Format: `[ ] US-###: [Story Title]`.
   - Place under `## STORIES ([Epic ID])`.

4. **First Story Setup**:
   - For the first User Story:
     - Define 2-5 technical tasks in `## TASKS ([Story ID])`.
     - Reset `## IMPLEMENTATION PIPELINE ([Story ID])` to all `[ ]`.

## Principles
- **Minimalism**: Focus only on the immediate next stories.
- **Traceability**: Link stories via ID (e.g., US-001 for EPIC-001).
- **Format Consistency**: Maintain the exact markdown structure of `master_todo.md`.

## Artefacts to Update (when promoting epics)

- Always update `./docs/todo/master_todo.md` as the source-of-truth. Record which other artefacts will be affected in the TODO entry's notes (e.g., `./.github/prompts/`, `./.github/skills/`, `./docs/`).
- When creating new user stories or tasks, create placeholder entries in `./docs/tasks/` and reference them in the master todo.
- Commit artefact edits separately using the `enh - chore:` prefix and include a short rationale.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/horschig) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
