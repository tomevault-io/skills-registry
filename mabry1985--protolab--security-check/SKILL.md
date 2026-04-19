---
name: security-check
description: Scans code for common security vulnerabilities and anti-patterns. Use when writing or reviewing code that handles user input, authentication, file access, database queries, or external APIs. Use when this capability is needed.
metadata:
  author: mabry1985
---

# Security Check

Scan the current codebase or recent changes for security issues.

## Focus areas:
1. **Input validation** — Are all user inputs validated before use?
2. **Injection** — SQL injection, command injection, path traversal, XSS
3. **Authentication** — Session handling, password storage, token management
4. **Secrets** — No hardcoded keys, tokens, passwords, or connection strings
5. **Dependencies** — Known vulnerabilities in packages

## Report findings by severity with specific file:line references.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mabry1985) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
