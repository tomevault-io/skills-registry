---
name: cli
description: CLI guidelines. Use whenever using the Bash tool, which is almost always. Also use when you see "command not found: __zoxide_z" errors. Use when this capability is needed.
metadata:
  author: motlin
---

# CLI Guidelines

## Directory Navigation

- I replaced `cd` with `zoxide`. Use `command cd` to change directories
    - This is the only command that needs to be prefixed with `command`
    - Don't prefix `git` with `command git`
- Try not to use `cd` or `zoxide` at all. It's usually not necessary with CLI commands
    - Don't run `cd <dir> && git <subcommand>`
    - Prefer `git -C <dir> <subcommand>`

## Flag Names

Prefer long flag names when available:

- Don't run `git commit -m`
- Run `git commit --message` instead

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/motlin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
