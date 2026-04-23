---
name: prime
description: This skill should be used when the user asks to "prime the project", "load context", "get oriented", "what is this project", or wants to understand the codebase structure before starting work. Use when this capability is needed.
metadata:
  author: ianphil
---

# Prime

Load project context by reading core documentation and listing the file structure.

## Workflow

1. Read `README.md` for project overview
2. If `.ainotes/memory.md` exists, read it for accumulated agent context
3. Run `git ls-files` to see the file structure
4. Note key directories and entry points

No summary needed - context is now loaded for subsequent work.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ianphil) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
