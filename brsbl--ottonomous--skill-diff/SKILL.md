---
name: skill-diff
description: Shows side-by-side diffs of skill changes. Generates HTML comparison of before/after for changed SKILL.md files. Use when reviewing skill changes. Use when this capability is needed.
metadata:
  author: brsbl
---

Generate visual diffs for changed skills, organized by scope.

## Scopes

| Scope | Comparison |
|-------|------------|
| **Uncommitted** | Working tree vs HEAD |
| **Staged** | Index vs HEAD |
| **Branch** | HEAD vs main |

## Usage

Run the script (auto-opens in browser):

```bash
node .claude/skills/skill-diff/scripts/skill-diff.js
```

Do NOT run `open` manually — the script handles it.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/brsbl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
