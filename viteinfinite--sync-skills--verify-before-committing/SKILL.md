---
name: verify-before-committing
description: Use when about to create a git commit in this project
metadata:
  author: viteinfinite
---
# Verify Before Committing

## Overview
CRITICAL: Always verify the following checklist before creating any git commit.

## Checklist

- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] `npm run build` has run and `./dist/` is up to date

## Common Mistakes
- Committing without running tests first
- Forgetting to build before committing TypeScript changes
- Committing with outdated dist folder

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/viteinfinite) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
