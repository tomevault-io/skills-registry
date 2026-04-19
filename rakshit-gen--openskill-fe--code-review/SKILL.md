---
name: code-review
description: Reviews code for quality issues, potential bugs, security vulnerabilities, and adherence to best practices. Provides actionable feedback with specific line references. Use when this capability is needed.
metadata:
  author: rakshit-gen
---

# code-review

Reviews code for quality issues, potential bugs, security vulnerabilities, and adherence to best practices. Provides actionable feedback with specific line references.

## Rules

- Always cite specific line numbers when referencing code issues
- Categorize issues by severity: critical, warning, suggestion
- Check for common security vulnerabilities (SQL injection, XSS, etc.)
- Verify error handling is comprehensive and appropriate
- Ensure code follows the project's established patterns and conventions
- Look for performance issues like N+1 queries, unnecessary loops
- Check for proper resource cleanup (file handles, connections)
- Verify tests cover the critical paths of new code

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakshit-gen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
