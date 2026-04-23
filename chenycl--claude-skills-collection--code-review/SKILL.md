---
name: code-review
description: > Use when this capability is needed.
metadata:
  author: chenycl
---

# Code Review Assistant

Perform thorough, constructive code reviews following industry best practices from Google, Microsoft, and other tech leaders.

## Review Checklist

### 1. Correctness
- [ ] Logic errors or edge cases not handled
- [ ] Off-by-one errors, null/undefined checks
- [ ] Race conditions or concurrency issues
- [ ] Error handling completeness

### 2. Security (OWASP Top 10)
- [ ] SQL injection, XSS, CSRF vulnerabilities
- [ ] Hardcoded secrets, credentials, API keys
- [ ] Input validation and sanitization
- [ ] Authentication/authorization flaws
- [ ] Sensitive data exposure

### 3. Performance
- [ ] N+1 queries, unnecessary loops
- [ ] Memory leaks, resource cleanup
- [ ] Inefficient algorithms (check Big-O)
- [ ] Unnecessary re-renders (React), recomputations

### 4. Maintainability
- [ ] Code readability and clarity
- [ ] Function/method length (< 30 lines ideal)
- [ ] Single Responsibility Principle
- [ ] DRY violations (Don't Repeat Yourself)
- [ ] Meaningful variable/function names

### 5. Testing
- [ ] Test coverage for new code
- [ ] Edge cases tested
- [ ] Mocking done correctly
- [ ] Integration tests where needed

### 6. Style & Conventions
- [ ] Follows project style guide
- [ ] Consistent formatting
- [ ] Appropriate comments (why, not what)
- [ ] No commented-out code

## Review Output Format

```markdown
## Code Review Summary

**Overall Assessment**: [APPROVE / REQUEST_CHANGES / COMMENT]
**Risk Level**: [Low / Medium / High / Critical]

### Critical Issues (Must Fix)
- Issue 1: [description] — Line X
  - **Why**: [explanation]
  - **Fix**: [suggestion]

### Suggestions (Should Consider)
- Suggestion 1: [description]

### Nitpicks (Optional)
- Nitpick 1: [minor style/preference]

### Positive Feedback
- [What's done well]
```

## Tone Guidelines

- Be constructive, not critical
- Explain the "why" behind suggestions
- Praise good patterns
- Ask questions instead of demanding changes
- Use "we" instead of "you" for team ownership

## Language-Specific Checks

### JavaScript/TypeScript
- Proper async/await, Promise handling
- Type safety (TypeScript strict mode)
- React hooks rules, dependency arrays
- Node.js: stream handling, event emitter cleanup

### Python
- Type hints usage
- Context managers for resources
- Generator usage where appropriate
- Pythonic idioms (list comprehensions, etc.)

### Go
- Error handling (don't ignore errors)
- Goroutine leaks, channel management
- defer usage for cleanup
- Interface design

### Rust
- Ownership and borrowing correctness
- Error handling with Result/Option
- Unsafe block justification
- Clippy warnings addressed

## Reference

See [references/review_guidelines.md](references/review_guidelines.md) for detailed review criteria per category.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chenycl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
