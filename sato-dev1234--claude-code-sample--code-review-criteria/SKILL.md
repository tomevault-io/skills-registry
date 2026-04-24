---
name: code-review-criteria
description: Code review criteria covering code quality, best practices, bugs, performance, and security concerns. Applied by code review and fix agents. Use when this capability is needed.
metadata:
  author: sato-dev1234
---

## Review Criteria

Review code changes and provide feedback on:
- Code quality and best practices
- Potential bugs or issues
- Performance considerations
- Security concerns
- Comment quality

Use CLAUDE.md for project-specific guidance.

## Severity Levels

### Critical (Must Fix)
- Security vulnerabilities (hardcoded credentials, SQL injection, XSS, missing input validation)
- Null pointer / missing null checks
- Resource leaks (unclosed connections, file handles)

### High (Code Quality)
- Large functions (> 50 lines)
- Deep nesting (> 4 levels)
- Missing error handling
- Absent test coverage

### Medium (Performance & Best Practices)
- Inefficient algorithms, N+1 queries
- Poor variable naming
- Missing documentation
- Inappropriate comments (design rationale, LLM traces)

## Fix Guidelines

- Fix security issues (remove hardcoded credentials, add input validation)
- Add null checks and error handling where missing
- Close unclosed resources (connections, file handles)
- Remove inappropriate comments (design rationale, LLM traces, etc.)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sato-dev1234) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
