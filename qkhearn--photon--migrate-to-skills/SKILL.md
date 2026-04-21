---
name: migrate-to-skills
description: Photon Skill Migrator: Convert legacy rules and commands to Photon's native Agent Skill format. Use when this capability is needed.
metadata:
  author: qkhearn
---

# Skill: Photon Skill Migrator
Convert legacy rules and slash commands to Photon's native Agent Skills format.

## Conversion Format
### Rules -> SKILL.md
Add `name` field, remove `globs`/`alwaysApply`, keep body exactly.

### Commands -> SKILL.md
Add frontmatter with `name` (from filename), `description` (infer from content), and `disable-model-invocation: true`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/qkhearn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
