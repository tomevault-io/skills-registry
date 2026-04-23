---
name: security-scan
description: Context-aware security audit based on OWASP Top 10. Scans for vulnerabilities in code and configuration. Includes proactive secret scanning for API keys and credentials. Use when this capability is needed.
metadata:
  author: hadimiftahulf
---
# Security Scan (The Hacker 🔐)

Merged: `security-scan` + `secret-guard`.

## When to Activate
- User mentions: "security", "vulnerability", "hack", "audit", "credentials".

## Scan Scope

### OWASP Top 10
1.  Injection (SQL, NoSQL, OS Command)
2.  Broken Authentication
3.  Sensitive Data Exposure
4.  XML External Entities (XXE)
5.  Broken Access Control (IDOR)
6.  Security Misconfiguration
7.  Cross-Site Scripting (XSS)
8.  Insecure Deserialization
9.  Using Components with Known Vulnerabilities
10. Insufficient Logging & Monitoring

### Secret Scanning (formerly secret-guard)
Proactively scan for leaked secrets:
- API Keys: `sk_live_`, `AKIA`, `ghp_`
- Private Keys: `BEGIN RSA PRIVATE KEY`, PEM files
- Database URLs: `postgres://`, `mysql://` with credentials
- Tokens: JWT, Bearer tokens in source code
- `.env` files committed to version control

## Rules
- NEVER allow secrets in committed code.
- Flag `.env` files not in `.gitignore`.
- Check for hardcoded credentials in config files.
- Recommend environment variable injection for all secrets.

## Cost: High (Explicit Trigger Required)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hadimiftahulf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
