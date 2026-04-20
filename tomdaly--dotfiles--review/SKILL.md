---
name: review
description: Review a GitHub PR with code analysis, JIRA validation, and grouped suggestions. Pass "code only" to skip JIRA lookup. Use when this capability is needed.
metadata:
  author: tomdaly
---

# 👀 Review a GitHub PR

$ARGUMENTS

## mode

If arguments include "code only" or "code-only": skip JIRA lookup, review code changes only.
Otherwise: follow the full review flow below.

## full review flow

1. **Extract ticket** from PR title
2. **Fetch JIRA details** using the JIRA integration config (skip if "code only" mode)
3. **Validate against acceptance criteria** (skip if "code only" mode)
4. **Check code patterns** and conventions
5. **Flag QA requirements** if needed
6. **Provide detailed feedback** with line references — think hard about suggestions
  a. Group suggestions into major, minor, and nitpick suggestions
  b. All suggestions should reference a file and line number (NOT patch line number)
7. **Never auto-approve** — present findings and await human decision

## output format

- Approve/don't approve
- Major suggestions
- Minor suggestions
- Nitpick suggestions
- Other considerations

Focus only on the most essential suggestions and think hard about this. The output response should be in your own writing style.

## QA flags

Mark for manual testing if PR includes:
- New user-facing features
- UI/UX changes
- Database migrations
- API modifications
- Performance-critical code

Red flags:
- Test suite failures in unrelated areas
- Breaking changes to public APIs
- Database schema modifications needed
- Security concerns identified
- Performance degradation detected

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tomdaly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
