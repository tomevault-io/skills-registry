---
name: git
description: >- Use when this capability is needed.
metadata:
  author: videojs
---

# Git

Git workflow conventions for Video.js 10.

## Reference Material

| Task                  | Load                   |
| --------------------- | ---------------------- |
| Writing commits       | `references/commit.md` |
| Inferring scope       | `references/scope.md`  |
| Creating/updating PRs | `references/pr.md`     |
| Naming branches       | `references/branch.md` |

## Quick Reference

**Commit:** `type(scope): lowercase description`

**Branch:** `type/short-description`

**PR Title:** Same as commit (or `RFC:` / `Discovery:` prefix)

## Process

1. Create branch following naming convention
2. Make changes
3. Commit with conventional message
4. Push and create PR with proper description

For the `/commit-pr` command, all steps after branching are automated.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/videojs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
