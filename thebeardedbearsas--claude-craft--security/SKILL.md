---
name: security
description: Security guidelines and OWASP Top 10. Use when reviewing security, implementing authentication or authorization, hardening code, or discussing vulnerabilities. Use when this capability is needed.
metadata:
  author: thebeardedbearsas
---

# Security

This skill provides universal security guidelines and OWASP Top 10 best practices applicable to any technology stack.

See @REFERENCE.md for detailed documentation.

## Quick Reference

- **Validation**: Always server-side, never trust client input
- **Queries**: Parameterized only (no SQL concatenation)
- **Passwords**: Hash with bcrypt/Argon2 (never MD5/SHA1)
- **Secrets**: Environment variables or vault (never in code)
- **Headers**: CSP, X-Frame-Options, HSTS, nosniff

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thebeardedbearsas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
