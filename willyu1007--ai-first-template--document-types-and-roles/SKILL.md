---
name: document-types-and-roles
description: Document types and their roles in the repository. Keywords: documents, types, roles. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Document Types and Roles

This repository uses four primary document types, each with a distinct purpose and audience.

## README.md (Human-Facing)

- Purpose: Human-oriented overview and orientation
- Audience: Human maintainers and new contributors
- Contains: Project description, setup instructions, links to detailed docs
- AI Behavior:
  - Read for context only
  - No AI-specific behavior rules
  - Do not treat as authoritative for AI actions
  - Only reference if an AGENTS.md explicitly directs to a specific fact

## AGENTS.md (AI-Facing Strategy)

- Purpose: AI-facing strategy and scope definition
- Audience: AI systems (code models, assistants, task runners)
- Contains: Scope, allowed operations, boundaries, safety rules, relations to other AGENTS
- AI Behavior:
  - Must follow strategy defined in the most local applicable AGENTS.md
  - Start from the closest applicable AGENTS.md and obey its scope and restrictions
  - Navigate up/down/sideways as directed by the Relations section

### Standard Sections (Required)

Every AGENTS.md must include these sections:

| Section | Purpose |
|---------|---------|
| 1. Scope & Role | Which part of the repository and what decisions this doc guides |
| 2. Responsibilities & Boundaries | What is in scope, what is out of scope or forbidden |
| 3. Input & Output Conventions | What AI assumes and what it produces (optional: can be simplified) |
| 4. Typical Decisions | Common questions and reasoning patterns for this scope |
| 5. Safety & Restrictions | Forbidden actions, high-risk areas, required precautions |
| 6. Relations to Other AGENTS.md | Upward, downward, and sideways navigation |

## SSOT Docs (Skill Packages)

- Purpose: Single Source of Truth (SSOT) for durable, AI-facing guidance
- Audience: AI systems
- Contains: SSOT skill entrypoints (`SKILL.md`) plus supporting docs (supporting files, `examples/`, etc.)
- AI Behavior:
  - Reference for understanding concepts and patterns
  - Discover via skills/workflows (start with the `repo-routing` skill if unsure)
  - Do not duplicate content in AGENTS.md

### Required YAML Front Matter

Only SSOT skill entrypoints (`SKILL.md`) require YAML front matter. Supporting docs do not require a global metadata contract.

```yaml
---
name: <skill-name>
description: One-line trigger description with keywords
---
```

### Style Requirements (Knowledge)

- Write for AI consumption: concise, structured, do/dont callouts where helpful.
- Prefer abstract flows and constraints over platform-specific commands.
- Keep AGENTS.md as strategy; keep SSOT content in skill packages (avoid duplication).

## Workdocs (workdocs/ directories)

- Purpose: Scenario-local task state
- Audience: AI systems during active work
- Contains: Plans, todos, context, outcomes
- AI Behavior:
  - Read/write during task execution
  - Create for non-trivial, multi-step, or cross-module tasks
  - Archive when work is complete; promote stable outcomes into the relevant skill package under `/.system/skills/ssot/**`

### Standard Workdocs Structure

Workdocs use a "required core + optional extensions" model to support both simple tasks and complex scenarios.

Required core (minimum 3 files):

```text
workdocs/active/T-YYYYMMDD-slug/
  plan.md       # Current approach and next steps
  context.md    # Constraints, assumptions, key pointers
  tasks.md      # Granular TODOs and checkpoints (verifiable items)
```

Optional extensions (add as needed for complex scenarios):

| File | Purpose | When to Use |
|------|---------|-------------|
| preparation.md | Prerequisites, assumptions, environment setup | Complex multi-phase work |
| outcome.md | Results, retrospectives, follow-ups | Work requiring documented outcomes |
| task.md | Single-sentence task description | Quick reference for current focus |

Naming rules:

- Use tasks.md (plural) for the verifiable checklist; do not use task.md for checklists.
- Use task.md (singular) only for a brief task description, not for checklists.
- All workdocs file names use lower_snake_case.md

## Related documents

- /.system/skills/ssot/repo/architecture-core-mechanisms/knowledge-metadata/SKILL.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
