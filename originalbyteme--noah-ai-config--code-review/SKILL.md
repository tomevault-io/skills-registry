---
name: code-review
description: Comprehensive code review for quality, security, and performance Use when this capability is needed.
metadata:
  author: originalbyteme
---

# Code Review Skill

Perform thorough code reviews focusing on quality, security, and maintainability.

## Review Checklist

### Security
- [ ] No hardcoded secrets or credentials
- [ ] Input validation on all external data
- [ ] Proper authentication/authorization checks
- [ ] No SQL/XSS/command injection vulnerabilities

### Quality
- [ ] Clear, descriptive naming
- [ ] Functions are focused and small
- [ ] Proper error handling
- [ ] No code duplication

### Performance
- [ ] Efficient algorithms (no unnecessary O(n²))
- [ ] Proper resource cleanup
- [ ] No memory leaks
- [ ] Optimized database queries

## Output Format

Organize findings by severity:
1. **Critical**: Must fix before merge
2. **High**: Should fix before merge
3. **Medium**: Consider fixing
4. **Low**: Nice to have improvements

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/originalbyteme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
