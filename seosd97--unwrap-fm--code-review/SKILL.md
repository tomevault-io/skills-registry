---
name: code-review
description: Review code and PRs. Use when reviewing pull requests, code changes, or providing feedback. Use when this capability is needed.
metadata:
  author: seosd97
---

# Code Review

## Checklist

- [ ] Follows project conventions
- [ ] No TypeScript errors
- [ ] No unused imports/variables
- [ ] Proper error handling
- [ ] No hardcoded values (use constants/env)
- [ ] Components are reusable
- [ ] No security issues (XSS, injection, etc.)

## Review Format

```markdown
## Summary
Brief overview of changes.

## Feedback
- **Issue**: Description
  - Suggestion: How to fix

## Approval
- [ ] Approved
- [ ] Request changes
```

## Priority Labels

| Label | Meaning |
|-------|---------|
| 🔴 Critical | Must fix before merge |
| 🟡 Suggestion | Nice to have |
| 🟢 Nitpick | Minor style issue |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seosd97) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
