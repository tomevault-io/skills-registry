---
name: github
description: Work with GitHub repositories using git and the web tools. Use when this capability is needed.
metadata:
  author: epappas
---

# GitHub

Use this skill to inspect repositories, check status, and review changes.

Guidelines:
- Prefer `rg` for searching code.
- Use `git status`, `git diff`, and `git log` for local changes.
- Use `web_search` and `web_fetch` when you need external context.

Examples:
```
rg -n "TODO" -S .
git status
git diff
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/epappas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
