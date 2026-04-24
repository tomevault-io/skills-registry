---
name: skill-sync
description: Synchronize AGENTS.md with the current state of available skills. Use after adding/removing skills. Use when this capability is needed.
metadata:
  author: reeinharddd
---

# Skill Sync Skill

> **Purpose:** Keep the central `AGENTS.md` orchestrator in sync with the `skills/` directory.

## Process

1.  **List Skills:** Scan `.github/skills/` for all `SKILL.md` files.
2.  **Extract Meta:** Read frontmatter `name` and `description` from each.
3.  **Update Table:** Regenerate the "Skills" table in `AGENTS.md`.

## Table Format

| Skill        | Description                  | File                                                                     |
| :----------- | :--------------------------- | :----------------------------------------------------------------------- |
| `skill-name` | Description from frontmatter | [.github/skills/skill-name/SKILL.md](.github/skills/skill-name/SKILL.md) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reeinharddd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
