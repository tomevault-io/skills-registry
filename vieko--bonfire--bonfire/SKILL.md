---
name: bonfire
description: Session context persistence for AI coding. Pick up exactly where you left off. Use when this capability is needed.
metadata:
  author: vieko
---

# Bonfire

Session context persistence for AI coding. Pick up exactly where you left off.

Git root: !`git rev-parse --show-toplevel`

## Routing

Parse `$ARGUMENTS` to determine action:

| Input | Action |
|-------|--------|
| `start` | Read [commands/start.md](commands/start.md) and execute |
| `end` | Read [commands/end.md](commands/end.md) and execute |
| Empty or context question | Read `<git-root>/.bonfire/index.md`, summarize relevant context, answer |

## Bootstrap

If `.bonfire/index.md` doesn't exist when any command runs, create defaults from [templates/](templates/): `.bonfire/index.md` (session context) and `.bonfire/.gitignore` (ignore all).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vieko) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
