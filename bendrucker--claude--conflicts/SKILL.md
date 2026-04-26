---
name: gitconflicts
description: Resolving git merge conflicts. Use when rebasing, merging, or cherry-picking results in conflicts. Use when this capability is needed.
metadata:
  author: bendrucker
---

# Git Conflicts

## Status

!`bun ${CLAUDE_PLUGIN_ROOT}/skills/conflicts/scripts/status.ts`

## Context

!`bun ${CLAUDE_PLUGIN_ROOT}/skills/conflicts/scripts/context.ts`

## Upstream

!`bun ${CLAUDE_PLUGIN_ROOT}/skills/conflicts/scripts/upstream.ts`

## Three-Way Access

Git stores three versions in staging slots during conflicts:

| Slot | Version | Command |
|------|---------|---------|
| `:1:path` | Base (common ancestor) | `git show :1:path` |
| `:2:path` | Ours (HEAD) | `git show :2:path` |
| `:3:path` | Theirs (incoming) | `git show :3:path` |

## References

- [rerere.md](references/rerere.md) — Automatic resolution reuse for repeated rebases

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bendrucker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
