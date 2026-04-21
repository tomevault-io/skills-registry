---
name: security-review
description: Audit code for exposed keys, weak logic, and common vulnerabilities. Covers validation, authentication, and data protection. Use when this capability is needed.
metadata:
  author: frost-guy-2006
---

# Security Review

> **Status**: Value placeholder. Original source could not be fetched.
> **Goal**: Prevent data breaches and secure user data.

## Audit Checklist
- [ ] **Secrets**: No hardcoded API keys or secrets in repo.
- [ ] **Input Validation**: Validate all user input (backend & frontend).
- [ ] **Authentication**: Verify session management logic.
- [ ] **Authorization**: Check permission checks for sensitive actions.
- [ ] **Data exposure**: Ensure PII is not logged.
- [ ] **Dependencies**: Check for known vulnerabilities in packages.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/frost-guy-2006) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
