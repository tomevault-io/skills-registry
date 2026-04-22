---
name: security-audit
description: Perform a security audit of the codebase. Checks for OWASP Top 10, AI-specific vulnerabilities, dependency issues, and configuration problems. Use when this capability is needed.
metadata:
  author: yu-iskw
---

# Security Audit

Perform a security audit with the following scope:

$ARGUMENTS

## Audit Methodology

### 1. Dependency Security

```bash
pnpm audit
```

Review all known vulnerabilities in dependencies.

### 2. Source Code Analysis

Scan for common vulnerability patterns:

- Hardcoded secrets (API keys, passwords, tokens)
- Command injection via string interpolation in Bash/exec calls
- XSS vectors in Vue.js templates (v-html usage)
- Prompt injection in AI agent inputs
- Insecure deserialization
- Information disclosure in error messages

### 3. AI Agent Security

Review the secure_agent pattern and verify:

- Input sanitization is applied before LLM processing
- Output sanitization prevents data leakage
- Tool calls are validated and scoped
- Prompt injection defenses are in place

### 4. Configuration Security

- No secrets in version control
- Proper .gitignore coverage
- CORS configuration
- Environment variable handling

## Output

Produce a security report with findings classified by severity:

- 🔴 Critical / 🟠 High / 🟡 Medium / 🔵 Low

Each finding includes: location, vulnerability, impact, and remediation steps.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yu-iskw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
