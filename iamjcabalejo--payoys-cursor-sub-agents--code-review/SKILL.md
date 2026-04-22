---
name: code-review
description: Review code for correctness, security, maintainability, and style. Use when reviewing pull requests, examining code changes, or performing code review. Use when this capability is needed.
metadata:
  author: iamjcabalejo
---

# Code Review

## Checklist
- [ ] **Correctness**: Logic correct, edge cases handled
- [ ] **Security**: No obvious vulnerabilities (injection, auth bypass)
- [ ] **Maintainability**: Readable, no unnecessary complexity
- [ ] **Tests**: Adequate coverage for changes
- [ ] **Style**: Matches project conventions

## Severity Levels
- **Critical**: Must fix (bug, security issue)
- **Suggestion**: Should improve (readability, pattern)
- **Nice to have**: Optional enhancement

## Feedback Format
- Be specific: point to the code
- Explain why: "Consider X because Y"
- Suggest fix when possible
- Acknowledge good patterns

## What to Avoid
- Nitpicking style (use linter)
- Vague feedback ("this could be better")
- Blocking on non-blocking issues

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iamjcabalejo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
