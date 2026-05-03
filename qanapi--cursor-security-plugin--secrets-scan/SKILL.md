---
name: secrets-scan
description: Scan code for hardcoded credentials, API keys, tokens, connection strings, and private keys. Use when checking a codebase for accidentally committed secrets. Use when this capability is needed.
metadata:
  author: qanapi
---

# Secrets Scan

## Overview

Scans a codebase for hardcoded credentials, API keys, tokens, connection strings, and private keys. Uses Grep to search for common secret patterns and checks .gitignore for proper exclusion of sensitive files.

## When to Use

- Checking a codebase for accidentally committed secrets
- Pre-commit or pre-merge validation
- Security hygiene review
- User explicitly requests a secrets scan or credential check

## Instructions

### 1. Use Grep to Search for Secret Patterns

Run targeted searches for:

| Pattern Type | Grep Pattern / Examples |
|--------------|--------------------------|
| **AWS keys** | `AKIA[0-9A-Z]{16}`, `aws_access_key`, `aws_secret` |
| **GCP** | `"type": "service_account"`, `private_key_id`, `client_email` in JSON |
| **Azure** | `DefaultEndpointsProtocol`, `AccountKey=`, `SharedAccessKey` |
| **API keys/tokens** | `sk-[a-zA-Z0-9]{20,}`, `ghp_[a-zA-Z0-9]{36}`, `glpat-[a-zA-Z0-9\-]{20,}`, `xoxb-`, `Bearer\s+[A-Za-z0-9\-_\.]+` |
| **DB connection strings** | `mysql://`, `postgres://`, `mongodb://`, `ConnectionString`, password in URL |
| **Private keys** | `BEGIN RSA PRIVATE KEY`, `BEGIN EC PRIVATE KEY`, `BEGIN OPENSSH PRIVATE KEY` |
| **Password/secret assignments** | `password\s*=\s*["'][^"']+["']`, `secret\s*=\s*["']`, `api_key\s*=\s*["']`, `token\s*=\s*["']` |
| **High-entropy strings** | Base64-like strings (long alphanumeric) assigned to `credential`, `key`, `secret` variables |
| **Committed .env** | `.env` in repo (not in .gitignore) |
| **JWT tokens** | `eyJ[a-zA-Z0-9_-]*\.eyJ[a-zA-Z0-9_-]*\.[a-zA-Z0-9_-]*` |

### 2. Check .gitignore

Verify .gitignore excludes:

- `.env`, `.env.local`, `.env.*.local`
- `*.pem`, `*.key`, `*.p12`, `*.pfx`
- `credentials.json`, `service-account*.json`
- `secrets/`, `config/secrets/`
- Any project-specific secret file patterns

Flag if sensitive patterns are **not** in .gitignore.

### 3. Report Each Finding

For each match, report:

- **File path**
- **Line number**
- **Type of secret** (e.g., AWS key, API token, DB password)
- **Severity**: Critical (live credential), High (credential pattern), Medium (suspicious), Low (false positive candidate)
- **Remediation**: Move to env var / secret manager, rotate the exposed credential immediately

### 4. Output Summary

- **Total secrets found** by type (e.g., 2 AWS keys, 1 API token, 3 password assignments)
- **Files requiring attention** (list of paths)

### 5. Compromise Warning

Include a clear warning:

> **Warning:** Any secret found in source code should be considered **compromised** and **rotated immediately**. Remove it from the codebase and use environment variables or a secret manager (e.g., Vault, AWS Secrets Manager, Doppler).

## Output Format

```markdown
# Secrets Scan Report

**Target:** [path or scope]
**Date:** [date]

## Findings

| File | Line | Type | Severity | Remediation |
|------|------|------|----------|-------------|
| ... | ... | ... | ... | ... |

## .gitignore Check

- [ ] .env excluded
- [ ] *.pem, *.key excluded
- [ ] credentials.json excluded
- [Issues if any]

## Summary

- **Total by type:** X AWS keys, X API tokens, X passwords, ...
- **Files requiring attention:** [list]

---

⚠️ **Warning:** Any secret found in source code should be considered **compromised** and **rotated immediately**. Remove from codebase and use environment variables or a secret manager.
```

## References

- [GitLeaks patterns](https://github.com/gitleaks/gitleaks)
- [TruffleHog](https://github.com/trufflesecurity/trufflehog)
- [OWASP Secrets Management](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/qanapi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
