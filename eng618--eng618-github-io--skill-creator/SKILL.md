---
name: skill-creator
description: Use when working with a meta-skill to guide the creation of new agent skills
metadata:
  author: eng618
---

# Skill Creator

Use this skill when you need to create a new agent skill.

## Required Structure

Each skill must be a directory in `.agent/skills/` containing at least a `SKILL.md` file.

## SKILL.md Format

The `SKILL.md` file MUST start with YAML frontmatter:

```yaml
---
name: skill-name
description: a brief description of what the skill does
---
```

## Guidance

1. **Focused Scope**: Each skill should have a clear, specific purpose.
2. **Actionable Instructions**: Provide clear steps or rules for the agent to follow.
3. **Resources**: If the skill requires helper scripts, place them in a `scripts/` subdirectory within the skill folder.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eng618) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
