---
name: code-review
description: Review code for quality, security, and maintainability. Use when reviewing pull requests, examining code changes, doing code review, QA checks, or architecture review for COMPLEX tasks. Includes QA checklist and CTO review template. Use when this capability is needed.
metadata:
  author: dmitryprg-ai
---

# Code Review

## QA Checklist (every review)

### Code Quality
- [ ] 0 linter errors
- [ ] 0 TypeScript errors
- [ ] No console.log (except debug)
- [ ] Tests pass
- [ ] Edge cases handled
- [ ] Error cases handled

### Naming Standards

| Type | Convention | Example |
|------|-----------|---------|
| Files (components) | PascalCase | `ComponentName.tsx` |
| Files (utils) | camelCase | `utilName.ts` |
| Functions | camelCase | `functionName()` |
| Components | PascalCase | `ComponentName` |
| Constants | UPPER_SNAKE | `CONSTANT_NAME` |

### JTBD Verification (for user-facing features)

- [ ] Job Story implemented
- [ ] UI text about benefits, not features
- [ ] Minimum steps to result
- [ ] No confusing terminology

## Feedback Format

- **CRITICAL**: Must fix before merge
- **SUGGESTION**: Consider improving
- **NICE TO HAVE**: Optional enhancement

## CTO Review (for COMPLEX tasks)

For architecture decisions, use the full template in [CTO-REVIEW.md](references/CTO-REVIEW.md).

Apply CTO Review when:
- Change affects >5 files
- API change used by others
- Data migration
- New architectural concept

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dmitryprg-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
