---
name: wa-security-review
description: > Use when this capability is needed.
metadata:
  author: brendankowitz
---

# Well-Architected Security Review

Conduct a focused security audit based on the Well-Architected Framework Security pillar.

**Usage**: When user says "security review", "wa security", or "security audit"

## Security Pillar Focus Areas

- Authentication & Authorization (identity management, RBAC)
- Data Protection (encryption at rest/transit, PII handling)
- Input Validation (SQL injection, XSS, command injection prevention)
- Secrets Management (no hardcoded credentials)
- Security Headers & Configuration

## Analysis Targets

Analyze the codebase for:
- 🚨 Critical security vulnerabilities (hardcoded secrets, SQL injection risks)
- ⚠️ Security weaknesses (missing authorization, weak encryption)
- ✅ Security best practices observed

## Output

Provide specific file references and actionable remediation steps.

Use the Well-Architected Agent with security pillar deep dive scope.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/brendankowitz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
