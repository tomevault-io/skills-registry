---
name: commit-rules
description: When making a Git commit, provide the subject of the commit and the rules to follow before committing. Use when this capability is needed.
metadata:
  author: t0k0sh1
---

This skill provides guidelines for commit targets and pre-commit checks.

## Commit targets

- All files managed by the repository are subject to commit.
- Please include files in the `dist/` directory in the commit target (as this is required when importing by specifying the repository directly).

## Instructions

1. Run `pnpm build` to ensure there are no build errors
2. Run `pnpm lint` to ensure there are no lint errors
3. Run `pnpm test` to ensure all tests pass
4. If steps 1 through 3 all pass, commit the changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t0k0sh1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
