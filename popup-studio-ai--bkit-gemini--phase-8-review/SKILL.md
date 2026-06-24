---
name: phase-8-review
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# Phase 8: Review

> Verify quality before deployment

## Review Types

### 1. Code Review

Check for:
- Code quality and readability
- Naming convention compliance
- Error handling
- Performance issues
- Security vulnerabilities

### 2. Architecture Review

Verify:
- Design document alignment
- Component structure
- API contract compliance
- Database schema match

### 3. Gap Analysis

Compare:
- Plan vs Implementation
- Design vs Code
- Expected vs Actual behavior

## Review Process

```
1. Run code analysis
   /code-review src/

2. Run gap analysis
   /pdca analyze {feature}

3. Check match rate
   - >= 90%: Ready for deployment
   - < 90%: Needs iteration

4. Iterate if needed
   /pdca iterate {feature}
```

## Checklist

### Code Quality
- [ ] No ESLint errors
- [ ] No TypeScript errors
- [ ] Tests passing
- [ ] Coverage adequate

### Architecture
- [ ] Follows design document
- [ ] API contracts match
- [ ] Data models aligned

### Security
- [ ] Input validation
- [ ] Authentication working
- [ ] No exposed secrets

## Next Phase

When match rate >= 90%: `/phase-9-deployment`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
