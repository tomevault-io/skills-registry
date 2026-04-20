---
name: skill-authoring
description: Create, validate, and maintain OpenCode skills (SKILL.md) for this repo. Use when this capability is needed.
metadata:
  author: basic-bit
---

## Purpose

Create durable, repo-local "skills" for OpenCode so repeating workflows (UI visuals, ops, releases, triage) are consistent and safe.

## What a skill is

- A skill is a folder containing a `SKILL.md` file.
- Skills are discovered and loaded via the `skill` tool.
- Skills should be reusable playbooks, not one-off notes.

## Where skills live

- `.opencode/skills/<skill-name>/SKILL.md`

## Requirements

- File name must be exactly `SKILL.md`.
- Must start with YAML frontmatter.
- Frontmatter must include:
  - `name`
  - `description`

## Naming rules

`name` must:

- be lowercase alphanumeric with single hyphen separators
- match the containing directory name
- be 1-64 characters

Regex: `^[a-z0-9]+(-[a-z0-9]+)*$`

## Content guidelines

- Prefer checklists + commands over long prose.
- Include sharp edges explicitly (credentials, webhooks, deploys, destructive commands).
- Never include secrets; document env var names and where they should be set.
- Include verification steps (how to confirm the workflow worked).

## Verification

After adding/editing a skill:

1. Ensure file exists at `.opencode/skills/<name>/SKILL.md`.
2. Ensure `name` matches the folder.
3. Restart OpenCode (skills are typically discovered at startup).
4. Load the skill via the `skill` tool.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/basic-bit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
