---
name: ralph-security-audit
description: Run comprehensive security audit combining multiple scanners (npm audit, pip-audit, semgrep, trivy) with secret detection, dependency vulnerability scanning, and OWASP Top 10 checks. Use for security review before deployment or during PR review. Use when this capability is needed.
metadata:
  author: kimhons
---

## Ralph Ultra Security Audit

Comprehensive multi-scanner security analysis.

### What this does

1. **Dependency scanning** — npm audit, pip-audit, cargo-audit
2. **Static analysis** — semgrep with auto-config rules
3. **Container scanning** — trivy for Docker images
4. **Secret detection** — Regex-based scan for API keys, tokens, passwords
5. **OWASP Top 10** — Check for common vulnerability patterns
6. **License audit** — Flag copyleft licenses in commercial projects

### Usage

```
/ralph-ultra:ralph-security-audit [--fix] [--severity critical,high]
```

### Options

| Option | Description |
|--------|-------------|
| `--fix` | Auto-fix safe vulnerabilities (patch updates) |
| `--severity` | Only report issues at specified levels |

### Severity Levels

- **CRITICAL** — Immediate action required (RCE, auth bypass)
- **HIGH** — Fix before deployment (SQL injection, XSS)
- **MEDIUM** — Fix in next sprint (info disclosure, CSRF)
- **LOW** — Track for later (deprecated APIs, minor config issues)

### Scanners Used

Runs whichever tools are available:
- `npm audit` / `yarn audit` — Node.js dependencies
- `pip-audit` / `safety` — Python dependencies
- `semgrep` — Multi-language SAST
- `trivy` — Container and filesystem scanning
- Built-in regex — Hardcoded secrets, insecure URLs, .env exposure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kimhons) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
