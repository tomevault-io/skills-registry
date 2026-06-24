---
name: a08-data-integrity-failures
description: Skills for exploiting software and data integrity failures including HTTP request smuggling per OWASP A08:2021. Use when this capability is needed.
metadata:
  author: omkar-ukirde
---

# Software and Data Integrity Failures (OWASP A08)

Violations of code and infrastructure integrity assumptions.

## Skills

- [HTTP Request Smuggling](references/http-request-smuggling.md) - Request desync attacks

## Quick Reference

| Technique | Frontend | Backend |
|-----------|----------|---------|
| CL.TE | Content-Length | Transfer-Encoding |
| TE.CL | Transfer-Encoding | Content-Length |
| TE.TE | Both with obfuscation | |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/omkar-ukirde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
