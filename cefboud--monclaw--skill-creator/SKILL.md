---
name: skill-creator
description: Create or update OpenCode skills under .agents/skills with concise, reusable instructions and minimal structure. Use when this capability is needed.
metadata:
  author: cefboud
---

# Skill Creator

Use this when asked to create or update a skill.

## Goal

Create minimal, useful skills in `.agents/skills/<skill-name>/`.

## Required structure

- `SKILL.md` (required)
- Optional helper files only when necessary (`scripts/`, `references/`, `assets/`)

## Rules

- Keep instructions concise and practical.
- Prefer defaults over complex options.
- Avoid extra docs like README/CHANGELOG in skill folders.
- Write only information the assistant actually needs at runtime.

## Basic template

```md
---
name: <skill-name>
description: <when to use this skill>
---

# <Title>

## When to use

<clear trigger conditions>

## Workflow

1. <step>
2. <step>

## Output

<expected result>
```

## Update behavior

When updating existing skills:
- Preserve current intent.
- Prefer small edits over rewrites.
- Keep backwards compatibility for expected file paths.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cefboud) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
