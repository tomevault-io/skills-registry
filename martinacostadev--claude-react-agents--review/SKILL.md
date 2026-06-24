---
name: review
description: Review code changes for quality, security, and best practices Use when this capability is needed.
metadata:
  author: martinacostadev
---

Review code changes following senior developer standards.

## Review Process

1. **Gather Context**
   - If PR number provided, run `gh pr view $ARGUMENTS --json files,body`
   - If file path provided, read the file
   - If nothing provided, run `git diff` for unstaged changes

2. **Analyze Changes**
   - Check for SOLID principle violations
   - Look for security issues
   - Identify performance concerns
   - Review TypeScript types
   - Check test coverage

3. **Generate Report**

## Review Checklist

### Code Quality
- [ ] Clear, descriptive naming
- [ ] Functions under 50 lines
- [ ] No code duplication
- [ ] Proper error handling

### TypeScript
- [ ] No `any` types
- [ ] Explicit return types
- [ ] Proper null handling

### React Patterns
- [ ] Hooks rules followed
- [ ] Proper dependency arrays
- [ ] No unnecessary re-renders

### Security
- [ ] Input validation
- [ ] No XSS vulnerabilities
- [ ] No exposed secrets

## Output Format

```markdown
## Code Review Summary

### Overview
[Brief description of changes]

### Strengths
- [What's done well]

### Issues
#### Critical
- [Must fix before merge]

#### Suggestions
- [Recommended improvements]

### Security
[Any security concerns]
```

Target: $ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/martinacostadev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
