---
name: security-best-practices
description: Security best practices for OWASP compliance and secure development Use when this capability is needed.
metadata:
  author: seqis
---

# Security Best Practices Skill

## Overview

Comprehensive security framework covering OWASP patterns, secure coding, vulnerability assessment, and security testing. Provides actionable guidance for both general application security and healthcare-specific compliance (HIPAA, medical devices).

**Core Mantra:** Trust nothing, validate everything, assume breach, minimize impact.

## Type

technique + domain

## When to Use

**Trigger this skill when:**
- Security review or audit requested
- Implementing authentication/authorization
- Handling sensitive data (PII, PHI, credentials)
- API endpoint design
- Code review for security concerns
- Vulnerability assessment needed
- Healthcare/medical application work
- Dependency or container security

**Keywords:** security, OWASP, vulnerability, injection, XSS, CSRF, authentication, authorization, encryption, PHI, HIPAA, penetration testing, secure coding

---

## OWASP Top 10 (2021) Quick Reference

| Rank | Vulnerability | Key Defense |
|------|--------------|-------------|
| A01 | Broken Access Control | Deny by default, verify on every request |
| A02 | Cryptographic Failures | AES-256, bcrypt/Argon2, TLS 1.2+ |
| A03 | Injection | Parameterized queries, input validation |
| A04 | Insecure Design | Threat modeling, secure design patterns |
| A05 | Security Misconfiguration | Hardened defaults, no debug in prod |
| A06 | Vulnerable Components | Dependency scanning, SBOM |
| A07 | Auth Failures | MFA, rate limiting, secure session mgmt |
| A08 | Data Integrity Failures | Code signing, integrity checks |
| A09 | Logging Failures | Audit logs, tamper-proof storage |
| A10 | SSRF | Allowlist URLs, validate redirects |

---

## Security Testing Pipeline

### Automated Scanning (Phase 1)
```bash
# Multi-tool security scan
npm audit --audit-level=moderate       # Node.js dependencies
safety check                           # Python dependencies
trivy image --severity HIGH,CRITICAL . # Container images
semgrep --config=auto .                # SAST analysis
truffleHog --regex --entropy .         # Secret detection
```

### Manual Code Review Focus (Phase 2)
- Input validation (whitelist approach)
- Authentication/authorization flows
- Data sanitization (SQL/XSS prevention)
- Cryptographic implementations
- Session management
- Error handling (no stack traces to users)

### Dynamic Testing (Phase 3)
- API endpoint fuzzing
- Authentication bypass attempts
- Authorization testing (IDOR, privilege escalation)
- Rate limiting verification
- CORS policy validation

---

## Critical Anti-Patterns (P0 - Fix Immediately)

### SQL Injection
```javascript
// BAD: String concatenation
const query = `SELECT * FROM users WHERE id = ${userId}`;

// GOOD: Parameterized queries
const query = 'SELECT * FROM users WHERE id = ?';
db.execute(query, [userId]);
```

### Hardcoded Secrets
```javascript
// BAD: In code
const API_KEY = "sk-1234567890abcdef";

// GOOD: Environment variables
const API_KEY = process.env.API_KEY;
```

### Weak Cryptography
```python
# BAD: MD5/SHA1 for passwords
hash = md5(password)

# GOOD: bcrypt/Argon2
hash = bcrypt.hash(password, 12)
```

### Missing Authentication
```javascript
// BAD: Unprotected endpoint
app.get('/admin', (req, res) => res.render('admin-panel'));

// GOOD: Auth required
app.get('/admin', requireAuth, requireRole('admin'), (req, res) => ...);
```

---

## Input Validation (Whitelist Approach)

```python
def validate_input(user_input, input_type):
    """Always validate and sanitize user input"""
    validators = {
        'email': r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$',
        'phone': r'^\+?1?\d{9,15}$',
        'alphanumeric': r'^[a-zA-Z0-9]+$',
        'uuid': r'^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$',
    }

    if input_type in validators:
        if not re.match(validators[input_type], user_input):
            raise ValidationError(f"Invalid {input_type}")

    if len(user_input) > MAX_INPUT_LENGTH:
        raise ValidationError("Input too long")

    return sanitized_input
```

---

## Security Headers Configuration

```javascript
// Express.js with helmet
const helmet = require('helmet');

app.use(helmet({
    contentSecurityPolicy: {
        directives: {
            defaultSrc: ["'self'"],
            scriptSrc: ["'self'"],
            styleSrc: ["'self'"],
            imgSrc: ["'self'", "data:", "https:"],
            objectSrc: ["'none'"],
            frameSrc: ["'none'"]
        }
    },
    hsts: { maxAge: 31536000, includeSubDomains: true, preload: true }
}));

// Additional headers
res.setHeader('X-Frame-Options', 'DENY');
res.setHeader('X-Content-Type-Options', 'nosniff');
res.setHeader('Referrer-Policy', 'strict-origin-when-cross-origin');
```

---

## Vulnerability Prioritization

```
CRITICAL (P0) - Fix Immediately:
- Remote code execution
- SQL injection in authentication
- Exposed secrets/API keys
- Missing auth on admin functions
- PHI exposure without encryption

HIGH (P1) - Fix Within 24 Hours:
- XSS in user-facing features
- CSRF on state-changing operations
- Insecure direct object references
- Weak cryptography in production

MEDIUM (P2) - Fix Within Sprint:
- Information disclosure
- Missing security headers
- Insufficient logging
- Outdated dependencies with CVEs

LOW (P3) - Track and Plan:
- Best practice violations
- Non-exploitable theoretical issues
```

---

## Healthcare Security (HIPAA)

For HIPAA regulatory requirements (PHI definitions, 18 identifiers, safeguard categories, breach notification), see `compliance-frameworks` skill and `compliance-frameworks/hipaa.md`.

### Implementation Patterns

| Requirement | Implementation |
|-------------|----------------|
| Access Control | RBAC with minimum necessary principle |
| Automatic Logoff | 15-min inactivity timeout |
| Encryption | AES-256 at rest, TLS 1.3 in transit |
| Audit Logging | Immutable logs with 6-year retention |

---

## Production Security Checklist

- [ ] All endpoints require authentication (except public)
- [ ] Rate limiting per user/IP
- [ ] Input validation on all parameters (whitelist)
- [ ] Output encoding prevents XSS
- [ ] CORS properly configured (no wildcard in prod)
- [ ] HTTPS enforced (HSTS enabled)
- [ ] Secrets in vault (not in code/repo)
- [ ] Dependency scanning in CI/CD
- [ ] Security headers configured
- [ ] Error messages don't leak system info
- [ ] Audit logging captures WHO/WHAT/WHEN/WHERE
- [ ] PHI encrypted at rest and in transit
- [ ] Penetration testing completed

---

## Breach Response (HIPAA)

### Assessment Factors
1. Nature and extent of PHI involved
2. Unauthorized person who accessed
3. Whether PHI was actually viewed/acquired
4. Extent of mitigation

### Notification Timeline
| Breach Size | Individuals | HHS | Media |
|-------------|-------------|-----|-------|
| >= 500 | 60 days | 60 days | Required |
| < 500 | 60 days | Year-end | Not required |

---

## Security Tools Reference

| Category | Tools |
|----------|-------|
| SAST | Semgrep, Bandit (Python), ESLint security plugins |
| DAST | OWASP ZAP, Burp Suite |
| Dependency | npm audit, safety, Snyk, Trivy |
| Secrets | TruffleHog, git-secrets, detect-secrets |
| Container | Trivy, Clair, Anchore |

---

## Related Skills

- `systematic-debugging` - For security bug investigation
- `architecture-patterns` - Secure design patterns
- `container-testing` - Container security validation

---

*Security is not a feature - it's a requirement. In healthcare, security failures can cost lives.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seqis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
