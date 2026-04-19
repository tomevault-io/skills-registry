---
name: task-pattern
description: Guidance for writing or refactoring agents and skills using structured task patterns. Use when creating new agents or skills, or refactoring existing ones. Use when this capability is needed.
metadata:
  author: roeia1
---

# Task Pattern for Agents and Skills

Patterns and best practices for writing or refactoring agents and skills using structured task tracking.

## Task Table Pattern

When an agent or skill has multiple steps, define them in a structured table using `TaskCreate` fields:

| Column | Description | Required |
|--------|-------------|----------|
| Subject | Brief task title (imperative form) | Yes |
| Description | Complete instructions to execute the task | Yes |
| Active Form | Present continuous form for spinner display | Yes |
| Blocked By | Tasks that must complete first | No |
| Blocks | Tasks waiting on this one | No |

### Example Table

```markdown
| Subject | Description | Active Form | Blocked By | Blocks |
|---------|-------------|-------------|------------|--------|
| Read config | Read `config.json` from project root using the Read tool. Expected structure: `{ "name": string, "version": string, "targets": string[] }`. If file doesn't exist, report error and stop. | Reading config | - | Validate config |
| Validate config | Check that all required fields exist: (1) `name` must be non-empty string, (2) `version` must match semver format `X.Y.Z`, (3) `targets` must be non-empty array. Report all validation errors before stopping. | Validating config | Read config | Apply changes |
| Apply changes | For each path in `targets` array: read the file, replace `{{NAME}}` with config name and `{{VERSION}}` with config version, write the file back. Log each file updated. | Applying changes | Validate config | - |
```

## Description Column

The description column must be self-contained with ALL information needed to execute the task:

- **Commands**: Include full command syntax with all flags and placeholders
- **Code snippets**: Embed directly, don't summarize or abbreviate
- **Output formats**: Specify exact structure with example field values
- **Step sequences**: Use numbered lists for multi-step procedures
- **Context**: Explain what things are (e.g., "review body comments are general feedback not tied to specific lines")

Never reduce or summarize existing content when converting to task table format. If the original documentation had examples, include them. If it had explanations, keep them.

References to external files (other documents in the repo) are acceptable, but never reference sections within the same file.

## Dependency Design

Design dependencies to maximize parallel execution where possible:

```
┌─────────┐  ┌─────────┐  ┌─────────┐
│ Task A  │  │ Task B  │  │ Task C  │   ← Wave 1 (parallel)
└────┬────┘  └────┬────┘  └────┬────┘
     │            └─────┬──────┘
     │                  │
     ▼                  ▼
┌─────────┐       ┌─────────┐
│ Task D  │       │ Task E  │            ← Wave 2 (parallel)
└────┬────┘       └────┬────┘
     └────────┬────────┘
              │
              ▼
        ┌─────────┐
        │ Task F  │                      ← Wave 3
        └─────────┘
```

## Checklist

- [ ] Steps defined in a task table
- [ ] Each task has subject, description, and active form
- [ ] Descriptions contain ALL original information (commands, examples, formats, context)
- [ ] No internal section references (no `[see X](#section)` within same file)
- [ ] Dependencies defined where applicable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/roeia1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
