---
name: code-review-checklist
description: Auto-activates when user mentions code review, reviewing code, PR review, or checking code quality. Provides systematic code review process with TodoWrite checklist. Use when this capability is needed.
metadata:
  author: pascallammers
---

# Code Review Checklist

Systematic code review process ensuring quality, security, and maintainability.

## When This Activates

- User says: "review this code", "code review", "check this PR"
- Before creating/merging PR
- When reviewing changes

## Review Checklist (TodoWrite)

Create todos for each item:

### 1. Functionality
- [ ] Code does what it claims to do
- [ ] Edge cases handled
- [ ] Error handling present
- [ ] No obvious bugs

### 2. Code Quality
- [ ] Clear variable/function names
- [ ] Functions are single-purpose
- [ ] No code duplication
- [ ] Follows project conventions
- [ ] No commented-out code

### 3. Testing
- [ ] Tests exist and pass
- [ ] Tests cover new functionality
- [ ] Tests cover edge cases
- [ ] No skipped/disabled tests without reason

### 4. Security
- [ ] No hardcoded secrets/API keys
- [ ] Input validation present
- [ ] SQL injection prevented (parameterized queries)
- [ ] XSS prevented (sanitized output)
- [ ] Authentication/authorization checked

### 5. Performance
- [ ] No obvious performance issues
- [ ] Database queries optimized
- [ ] No N+1 query problems
- [ ] Large lists paginated
- [ ] Heavy operations async

### 6. Documentation
- [ ] Public APIs documented
- [ ] Complex logic explained
- [ ] README updated if needed
- [ ] CHANGELOG updated if needed

## Process

1. **Create TodoWrite checklist** from items above
2. **Mark in_progress** as you review each
3. **Add findings** as you discover issues
4. **Complete** when reviewed
5. **Present summary** with:
   - ✅ Approved items
   - ⚠️ Issues found
   - 🔴 Blockers

## Review Findings Format

```markdown
## Code Review Summary

### ✅ Strengths
- Clear naming conventions
- Good test coverage (85%)
- Error handling comprehensive

### ⚠️ Minor Issues
1. Line 45: Variable name `x` should be `userId`
2. Line 102: Missing error case for null input
3. Missing JSDoc comment on `processData` function

**Suggested fixes:** [code suggestions]

### 🔴 Blockers (MUST FIX)
1. Line 78: **Hardcoded API key** - Move to environment variable
2. Line 123: **SQL injection risk** - Use parameterized query

**These must be fixed before merge.**

### 📊 Metrics
- Files changed: 5
- Lines added: 230
- Lines removed: 45
- Test coverage: 85% → 88%
- Complexity: Medium

### 💡 Recommendations
- Consider extracting `validateUser` to shared util
- Add integration test for auth flow
- Document breaking changes in CHANGELOG
```

## Auto-Checks

Run automated checks:

```bash
# Lint
npm run lint

# Type check
npm run typecheck

# Tests
npm test

# Security scan
npm audit

# Check for secrets
git diff --cached | grep -i "api[_-]key\\|password\\|secret\\|token"
```

## Common Issues

| Issue | Fix |
|-------|-----|
| Hardcoded secrets | Move to `.env`, add to `.env.example` |
| SQL injection | Use parameterized queries |
| Missing tests | Add tests before merge |
| Complex function | Break into smaller functions |
| Magic numbers | Extract to named constants |
| No error handling | Add try/catch, validate inputs |

**Use TodoWrite to track all checklist items. Present summary when complete.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pascallammers) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
