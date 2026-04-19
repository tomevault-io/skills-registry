---
name: update-skill
description: Update existing skills while keeping them simple, coherent, and non-duplicative; always recommend the best placement for new rules. Use when this capability is needed.
metadata:
  author: fredeschmidt
---

# Update Skill

Use this skill when the user asks to update any skill file (`SKILL.md`, `AGENTS.md`, templates, references) and wants the result to stay clean and easy to maintain.

## Core Intent

Keep skills simple and clean without losing context.
Every update must fit the full skill flow, not just a local section.

## Non-Negotiable Rules

1. Read the whole target skill file before editing.
2. If related files exist for that skill (`AGENTS.md`, `TEMPLATE.md`, references), read the relevant ones before editing.
3. Do not append blindly. Integrate changes where they logically belong.
4. Avoid repetition:
   - no duplicate rules,
   - no repeated wording across sections unless required,
   - merge overlapping instructions.
5. Preserve structure clarity:
   - short sections,
   - predictable headings,
   - direct language,
   - minimal but sufficient detail.
6. Keep behavioral intent intact when refactoring wording.
7. Keep `HOW-TO.md` synchronized when skill or workflow changes affect task execution behavior.

## Placement Recommendation Rule (Mandatory)

Always recommend the best home for the requested update before applying it.
This recommendation is mandatory even when the user asks for a specific file.

Evaluate placement in this order:

1. Existing skill, existing section (best).
2. Existing skill, new section (if concept is new but belongs there).
3. Different existing skill (if concern belongs elsewhere).
4. New skill (only if concern is reusable and does not fit existing skills cleanly).

When requested file is likely wrong, explicitly say:
- why it is a mismatch,
- which file/skill is better,
- whether to move or duplicate (default: move, not duplicate).

## Decision Heuristics

Use these heuristics to decide where an update belongs:

- `build-component-figma`: source-specific flow, confirmations, branching, and implementation sequence for Figma-driven component builds.
- `architecture`: repository constraints, token governance, architecture boundaries, integration rules.
- `update-component`: updates to existing components from design references + change requests.
- `wrap-up`: git wrap-up flow (commit/push/merge-to-main/end-on-main).
- `update-skill` (this skill): how to maintain/edit skills cleanly.

Recommend a new skill only when:
- the rule is cross-cutting and reusable across many skills,
- it introduces a distinct workflow,
- adding it to an existing skill would create conceptual clutter.

## Workflow (Execute In Order)

1. Parse the requested update.
2. Read the entire target file(s) end-to-end.
3. Identify overlaps/conflicts/duplication risk.
4. Provide placement recommendation:
   - best file,
   - rationale,
   - note if requested target is suboptimal.
5. Apply edit in the recommended location.
6. Refactor nearby text to keep flow coherent.
7. Run a clean-up pass:
   - remove duplicates,
   - normalize terminology,
   - keep section length tight.
8. Report:
   - what changed,
   - why this location was chosen,
   - alternatives considered,
   - any follow-up suggestions.

## Cross-File Sync Rule (Mandatory)

When a skill update changes task flow, required inputs, ordering, validation, or reporting behavior:

1. Update `HOW-TO.md` in the same change.
2. Ensure skill role descriptions and execution order in `HOW-TO.md` still match current skills.
3. Mention the synchronization in the completion report.

## Output Requirements

When done, report:

1. Recommendation summary (best placement + reason).
2. Files changed.
3. Structural cleanup performed (if any).
4. Any remaining ambiguity and your recommended next step.
5. Whether `HOW-TO.md` was updated (and why, if not needed).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fredeschmidt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
