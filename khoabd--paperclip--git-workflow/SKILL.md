---
name: git-workflow
description: > Use when this capability is needed.
metadata:
  author: khoabd
---

# Git Workflow & Versioning

One commit = one logical change. Branch names describe work. PRs are small.

## Branch Strategy (GitHub Flow)

```
main ──────────────────────────────────── (always deployable)
       └── feature/ATO-123-add-login ── (short-lived)
       └── fix/ATO-456-null-pointer ──
       └── release/v1.2.0 ────────────  (optional for scheduled releases)
```

## Commit Message Format
```
type(scope): short description

Body explaining WHY (optional, 72 char wrap)

Refs: ATO-123
```
Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `ci`

## PR Checklist
- [ ] Branch named with issue ID: `feat/ATO-123-description`
- [ ] Commits are atomic (one logical change)
- [ ] PR description explains WHY not WHAT
- [ ] Tests pass
- [ ] No merge conflicts
- [ ] Linked to issue

## Versioning (SemVer)
- `MAJOR.MINOR.PATCH`
- MAJOR: breaking change
- MINOR: new feature, backward compatible
- PATCH: bug fix

## Red Flags
- Committing directly to main
- `git push --force` on shared branches
- Commits with message "fix" or "wip"
- PRs with 50+ files changed

---
> Source: [khoabd/paperclip](https://github.com/khoabd/paperclip) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
