---
name: security-hint
description: Notices potential security issues when viewing code with SQL queries, hardcoded credentials, API keys, user input handling, or authentication logic. Use when user shows or discusses code that might have security vulnerabilities like SQL injection, XSS, or exposed secrets. Use when this capability is needed.
metadata:
  author: alexeytriplea
---

# Security Hint

Lightweight security awareness for conversations. Provides quick hints about potential security issues without full analysis.

## When to activate

Activate when you notice in code being discussed:
- SQL queries using string concatenation
- Hardcoded passwords, API keys, tokens
- User input rendered without escaping
- `eval()` or dynamic code execution
- Sensitive data in `console.log`
- Missing authentication checks
- File paths from user input

## What to do

**Briefly mention** the concern in 1-2 sentences, then suggest running a command:

**Example responses:**

```
"I see SQL string concatenation on line 23, which could allow SQL injection.
Run `/review src/api/` to check for security vulnerabilities."
```

```
"There's a hardcoded API key on line 8. Consider moving it to environment variables.
Run `/quick-check src/config.js` for security scan."
```

```
"This code renders user input without escaping (line 34), potential XSS risk.
Run `/deep-review` for comprehensive security analysis."
```

## Important rules

- ❌ Do NOT perform full security audit
- ❌ Do NOT scan entire codebase
- ✅ Only mention issues you can see in the current conversation
- ✅ Keep hints brief (1-2 sentences)
- ✅ Always suggest a command for full analysis
- ✅ Be helpful, not alarmist

## Commands to suggest

- `/quick-check` - for fast security scan
- `/review [file]` - for specific file review
- `/deep-review` - for comprehensive analysis

This is a hint tool, not a replacement for proper security review.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexeytriplea) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
