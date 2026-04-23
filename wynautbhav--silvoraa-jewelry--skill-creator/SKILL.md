---
name: skill-creator
description: Use when working with a meta-skill for generating new agent skills. Use when the user wants to teach the agent a new capability or workflow.
metadata:
  author: wynautbhav
---

# Skill Creator Skill

## How to Create a Skill
1. **Identify the Need**: Is this a repeatable task? (e.g., "Writing SQL" or "Fixing CSS").
2. **Create Directory**: Make a folder `.agent/skills/<skill-name-kebab-case>/`.
3. **Create SKILL.md**: Create the file with the standard YAML frontmatter.

## SKILL.md Template
Always use this structure:
```markdown
---
name: <skill-name>
description: <One sentence description used for AI discovery>
---

# <Skill Name>

## When to use this skill
- Bullet points of scenarios.

## Instructions
- Step-by-step guide on how to execute the skill.
- Best practices and "Do nots".

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wynautbhav) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
