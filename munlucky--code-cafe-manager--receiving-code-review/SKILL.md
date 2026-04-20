---
name: receiving-code-review
description: Collects and organizes code review feedback into actionable tasks. Use when this capability is needed.
metadata:
  author: munlucky
---

# Receiving Code Review Skill

**Role**: Collect and organize code review feedback and track follow-up work.

## Inputs
- Review comments/issue links
- Related commit/PR links

## Behavior
1. Classify feedback (bug/requirement/style/question) and summarize.
2. Convert follow-up work into TODOs and reflect in `pending-questions.md` or the plan (context.md).
3. Record files/lines to check from the review.

## Output (example)
```markdown
# Review Summary
- Bug: missing fetch error handling -> `_fetch/executeRetry.client.ts`
- Requirement: add empty state message -> page.tsx
- Question: paging server or client? -> pending-questions.md
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/munlucky) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
