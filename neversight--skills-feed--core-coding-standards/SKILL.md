---
name: core-coding-standards
description: Universal code quality rules. Extends nothing — this is the base skill every project should include. Use when writing or reviewing any code. Use when this capability is needed.
metadata:
  author: neversight
---

# Principles

- Keep it simple (KISS) — prefer the simplest solution that works
- Don't repeat yourself (DRY) — extract when you see three duplicates, not before
- Single Responsibility — each module/function does one thing
- Use descriptive, intention-revealing names
- Use kebab-case for files and folders
- Functions should have clear inputs and outputs with minimal side effects
- Keep functions right-sized — extract when logic needs a comment to explain
- Delete dead code — don't comment it out
- Never swallow errors silently
- Measure before optimizing — no premature performance work
- No premature abstraction — wait for three concrete duplicates before extracting

# Rules

See `rules/` for detailed patterns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
