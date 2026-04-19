---
name: code-review
description: Review code quality, patterns, and best practices Use when this capability is needed.
metadata:
  author: duhenri9
---

# Code Review

## When to Use
Use this skill when reviewing code files or when asked to analyze code quality.

## Review Dimensions

### 1. Readability
- Clear naming conventions
- Appropriate comments (why, not what)
- Consistent formatting
- Logical code organization

### 2. Maintainability
- Single Responsibility Principle
- Low coupling, high cohesion
- No magic numbers/strings
- Configuration externalized

### 3. Correctness
- Logic errors
- Off-by-one errors
- Null/undefined handling
- Type safety

### 4. Performance
- Algorithm complexity
- Resource management
- Caching opportunities
- Unnecessary computations

### 5. Security
- Input validation
- Output encoding
- Authentication checks
- Authorization checks

## Output Format
```markdown
## File: [filename]

### Issues Found
| Line | Severity | Issue | Suggestion |
|------|----------|-------|------------|
| 42 | High | SQL injection risk | Use parameterized query |

### Positive Observations
- Good use of...
- Well-structured...

### Refactoring Opportunities
- Extract method for...
- Consider using pattern...
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duhenri9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
