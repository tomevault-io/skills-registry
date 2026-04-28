---
name: code-review
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# Code Review Skill

> Automated code quality analysis and review guidance.

## Actions

| Action | Description | Example |
|--------|-------------|---------|
| `review` | Full code review | `$code-review review src/` |
| `quick` | Quick review of a file | `$code-review quick src/app/page.tsx` |
| `security` | Security-focused review | `$code-review security` |

## Review Dimensions

### 1. Correctness
- Logic errors and bug patterns
- Edge case handling (null, empty, boundary values)
- Error handling completeness
- Type safety violations

### 2. Performance
- N+1 queries
- Unnecessary re-renders
- Missing memoization
- Large bundle imports
- Unoptimized images

### 3. Security
- Input validation
- XSS vulnerability
- SQL/NoSQL injection
- Exposed secrets
- Missing auth checks

### 4. Maintainability
- Code readability
- Naming conventions
- Function/file length
- DRY violations
- Dead code

### 5. Convention Compliance
- Project naming rules (Phase 2)
- File structure rules
- Import ordering
- Comment quality

## Review Output Format

```markdown
## Code Review: [filename]

### Critical Issues
- [Line XX] Description of critical issue

### Major Issues
- [Line XX] Description of major issue

### Minor Issues / Suggestions
- [Line XX] Suggestion for improvement

### Summary
- Files: X reviewed
- Issues: X critical, X major, X minor
- Recommendation: Approve / Changes Requested
```

## Integration with Pipeline

Code review is part of Phase 8 but can be run independently at any time.
Use `$phase-8-review` for a full architecture + code review.
Use `$code-review` for quick, file-level reviews during development.

## Review Checklist

See `references/review-checklist.md` for the complete checklist.

## Common Patterns to Flag

| Pattern | Issue | Fix |
|---------|-------|-----|
| `catch(e) {}` | Swallowed error | Log or re-throw |
| `any` type | Type safety loss | Use proper type |
| `// TODO` | Incomplete code | Complete or track |
| Hardcoded URL | Environment coupling | Use env vars |
| `console.log` | Debug code left in | Remove or use logger |
| `key={index}` | Unstable React key | Use unique ID |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
