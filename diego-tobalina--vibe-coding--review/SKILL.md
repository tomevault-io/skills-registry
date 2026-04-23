---
name: review
description: Code review for quality, security, and maintainability. Reviews recent git changes. Use when this capability is needed.
metadata:
  author: diego-tobalina
---

# Code Review

Review recent changes for quality, security, and maintainability.

## Review Process

1. Run `git diff` to see recent changes
2. Focus on modified files
3. Analyze changes against review checklist

## Review Checklist

### Code Quality
- [ ] Code is clear and readable
- [ ] Functions/methods are well-named (verb-first)
- [ ] No duplicated code
- [ ] Single responsibility principle followed
- [ ] Methods under 20 lines

### Error Handling
- [ ] All errors properly handled
- [ ] No silent failures
- [ ] Error messages are actionable
- [ ] Appropriate exception types used

### Security
- [ ] No hardcoded credentials/secrets
- [ ] Input validation on all user inputs
- [ ] No SQL injection vulnerabilities
- [ ] No XSS vulnerabilities
- [ ] Sensitive data not logged

### Testing
- [ ] Test coverage for new code
- [ ] Edge cases tested
- [ ] Tests follow naming convention

### Performance
- [ ] No N+1 query issues
- [ ] Appropriate algorithm complexity
- [ ] Proper use of caching where needed

## Output Format

Organize feedback by priority:

### 🔴 Critical (Must Fix)
Issues that will cause bugs, security vulnerabilities, or data loss.

### 🟡 Warnings (Should Fix)
Issues that affect maintainability or could cause problems.

### 🟢 Suggestions (Consider)
Improvements for readability or best practices.

For each issue, provide:
- File and line number
- Current code snippet
- Problem description
- Suggested fix

### Summary
Brief overview of changes reviewed.

### 🔴 Critical Issues
| File:Line | Issue | Fix |
|-----------|-------|-----|
| ... | ... | ... |

### 🟡 Warnings
| File:Line | Issue | Suggestion |
|-----------|-------|------------|
| ... | ... | ... |

### 🟢 Suggestions
- Suggestion 1
- Suggestion 2

### ✅ What's Good
- Positive observation 1
- Positive observation 2

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/diego-tobalina) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
