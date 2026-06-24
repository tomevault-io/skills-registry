---
name: example-skill
description: Demonstrates the skill file format Use when this capability is needed.
metadata:
  author: nicolasforstinger
---
# Example Skill

This is the skill body. It's injected into the system prompt when the agent
loads this skill via the `load_skill` tool, or when it's listed in `always_on`.

## Usage

Skills are markdown files with YAML frontmatter in `workspace/skills/<name>/SKILL.md`.

**Frontmatter fields:**
- `name` — Skill name (defaults to directory name if omitted)
- `description` — One-line summary shown in the skill index

**Body** — Full instructions, examples, or rules. Loaded on demand or always-on.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nicolasforstinger) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
