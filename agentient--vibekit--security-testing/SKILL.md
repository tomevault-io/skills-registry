---
name: security-testing
description: > Use when this capability is needed.
metadata:
  author: agentient
---

# Security Testing

> **STUB: This skill is not yet implemented**
>
> This placeholder preserves the documented plugin structure.
> See parent plugin README for planned capabilities.

## Planned Capabilities

- **Vulnerability Scanning**: Automated CVE detection in dependencies
- **SAST**: Static Application Security Testing patterns
- **DAST**: Dynamic Application Security Testing guidance
- Penetration testing methodology
- Security regression testing
- CI/CD security gate integration

## Anti-Patterns to Detect

- String concatenation for SQL queries
- No ownership verification for resource access
- Missing CSRF protection on state-changing operations
- Verbose error messages exposing stack traces
- Default/hardcoded credentials
- Disabled security features (CORS allow-all, CSRF disabled)
- Using `eval()` with user input
- Missing security headers (CSP, HSTS, X-Frame-Options)

## Implementation Status

- [ ] Core implementation
- [ ] References documentation
- [ ] Output templates
- [ ] Integration tests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentient) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
