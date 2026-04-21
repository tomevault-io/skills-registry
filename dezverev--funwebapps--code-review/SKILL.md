---
name: code-review
description: Conducts professional code reviews of branch changes and documents findings. Use when the user wants a code review, asks to review changes, check code quality, or before creating a pull request. Use when this capability is needed.
metadata:
  author: dezverev
---

# Code Review

Performs a professional code review of all changes on the current branch vs main and documents findings in `.reviews/`.

## Workflow

### Step 1: Identify Changes

Get all changes on current branch compared to main:

```bash
git diff main...HEAD --name-only    # List changed files
git diff main...HEAD                 # Full diff
```

### Step 2: Analyze Each File

For each changed file, review for:

- **Correctness** - Logic errors, edge cases, null checks
- **Security** - Injection, XSS, secrets, auth issues
- **Performance** - N+1 queries, unnecessary loops, memory leaks
- **Maintainability** - Readability, complexity, naming
- **Testing** - Coverage, edge cases, mocking
- **Style** - Consistency with project conventions

### Step 3: Check Documentation

Use memory and context7 for best practices:

```
search_nodes → Find relevant patterns/standards
query-docs → Get current library best practices
```

### Step 4: Create Review Document

Create review file in `.reviews/`:

```
.reviews/{branch-name}-review.md
```

**Document format:**

```markdown
# Code Review: {branch-name}

**Reviewer**: AI Assistant
**Date**: {date}
**Branch**: {branch} → main
**Files Changed**: {count}

---

## Summary

[Overall assessment - 2-3 sentences]

**Recommendation**: ✅ Approve | ⚠️ Approve with Comments | ❌ Request Changes

---

## Findings

### 🔴 Critical
[Must fix before merge - security issues, breaking bugs]

### 🟠 Major  
[Should fix - significant bugs, performance issues]

### 🟡 Minor
[Consider fixing - code quality, maintainability]

### 💡 Suggestion
[Optional improvements - style, optimization ideas]

---

## File-by-File Review

### `path/to/file.ts`

**Changes**: [Brief description]

| Line | Severity | Finding |
|------|----------|---------|
| 42 | 🔴 Critical | SQL injection vulnerability |
| 67 | 🟡 Minor | Consider extracting to helper function |

---

## Checklist

- [ ] No security vulnerabilities
- [ ] Error handling is comprehensive
- [ ] Tests cover new functionality
- [ ] No hardcoded secrets or credentials
- [ ] Code follows project conventions
- [ ] Performance considerations addressed

---

## Positive Notes

[Highlight good practices observed]
```

### Step 5: Commit Review

```bash
git add .reviews/
git commit -m "docs: add code review for {branch-name}"
```

## Severity Definitions

| Level | Icon | When to Use |
|-------|------|-------------|
| Critical | 🔴 | Security vulnerabilities, data loss, breaking bugs |
| Major | 🟠 | Significant bugs, performance issues, missing validation |
| Minor | 🟡 | Code quality, maintainability, minor bugs |
| Suggestion | 💡 | Style improvements, optional optimizations |

## Example Output

```markdown
# Code Review: feat/user-auth

**Reviewer**: AI Assistant
**Date**: 2026-01-31
**Branch**: feat/user-auth → main
**Files Changed**: 4

---

## Summary

Implements JWT authentication with good separation of concerns. 
Found one critical security issue with token storage and several 
minor improvements for error handling.

**Recommendation**: ⚠️ Approve with Comments

---

## Findings

### 🔴 Critical

1. **Token stored in localStorage** (src/dev/auth/storage.ts:23)
   - Vulnerable to XSS attacks
   - Use httpOnly cookies instead

### 🟠 Major

1. **No token expiration check** (src/dev/auth/validate.ts:45)
   - Expired tokens will pass validation

### 🟡 Minor

1. **Magic number for timeout** (src/dev/auth/session.ts:12)
   - Extract to config constant

### 💡 Suggestion

1. Consider using refresh token rotation pattern

---

## Positive Notes

- Clean separation of auth logic into modules
- Good use of TypeScript types
- Comprehensive error messages
```

## Additional Resources

For detailed review standards, see [standards.md](standards.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
