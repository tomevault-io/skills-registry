---
name: review
description: Review pull requests with detailed feedback on code quality, tests, and conventions. Use when maintainer wants to review a PR. Use when this capability is needed.
metadata:
  author: jay05410
---

# PR Review Skill (For Maintainers)

Perform comprehensive code review with constructive feedback.

## When to Use

- Maintainer asks to review a PR
- Maintainer provides a PR URL or number
- Maintainer asks "is this PR good?"
- Maintainer wants code review help

## Process

1. **Fetch PR details via GitHub MCP**
   - Get diff and changed files
   - Check commit history
   - Review linked issues
   - Check CI status

2. **Review checklist**
   - Functionality: Does it solve the issue?
   - Code quality: Readable, maintainable?
   - Tests: Adequate coverage?
   - Conventions: Follows project style?
   - Security: Any vulnerabilities?
   - Performance: Any concerns?

3. **Generate feedback**
   - Categorize: Required / Suggestion / Question / Nitpick
   - Provide code suggestions where helpful
   - Balance criticism with praise

## Output Format

Respond in user's language:

```
# PR Review: #[number] [title]

## Overview
| Aspect | Assessment |
|--------|------------|
| Decision | Approve / Request Changes / Comment |
| Risk | Low / Medium / High |
| Effort | Small / Medium / Large |

## Linked Issue
Resolves #[number]: [Yes/Partially/No]

## CI Status
- Build: ✅/❌
- Tests: ✅/❌
- Lint: ✅/❌

---

## ✅ Good Points
- [Specific praise with file reference]
- [Good practice observed]

## 🔴 Required Changes

### [file:line] [Short description]
**Issue**: [What's wrong]
**Suggestion**:
```[language]
[code fix]
```

## 🟡 Suggestions

### [file:line] [Description]
[Why this would be better]

## 🔵 Questions

### [file:line]
[Question seeking clarification]

---

## Summary Comment
```
Thanks for this PR!

**Required**:
- [ ] [Change 1]

**Optional**:
- [ ] [Suggestion 1]

[Closing note]
```
```

## Arguments

`$ARGUMENTS` can include:
- PR number: `#456`
- PR URL: `https://github.com/owner/repo/pull/456`
- Focus: `--focus=security`, `--focus=performance`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jay05410) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
