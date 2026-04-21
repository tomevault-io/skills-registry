---
name: review-security
description: Comprehensive security review for web applications Use when this capability is needed.
metadata:
  author: luanps2
---

# Review Security Skill

## OWASP Top 10 Checklist

### 1. Injection (SQL, NoSQL, Command)
- [ ] Parameterized queries (no string concatenation)
- [ ] Input validation and sanitization
- [ ] ORM usage (Prisma, TypeORM)

### 2. Broken Authentication
- [ ] Strong password hashing (bcrypt, argon2)
- [ ] Secure session management
- [ ] Multi-factor authentication (if applicable)

### 3. Sensitive Data Exposure
- [ ] HTTPS enforced
- [ ] Sensitive data encrypted at rest
- [ ] No sensitive data in logs
- [ ] Secure cookie flags (httpOnly, secure, sameSite)

### 4. XML External Entities (XXE)
- [ ] XML parsing disabled or secured
- [ ] JSON used instead of XML (where possible)

### 5. Broken Access Control
- [ ] Authorization checks on every protected route
- [ ] Resource ownership validated
- [ ] Least privilege principle

### 6. Security Misconfiguration
- [ ] No default credentials
- [ ] Error messages don't expose internals
- [ ] Security headers configured (CSP, X-Frame-Options)
- [ ] Unnecessary features disabled

### 7. Cross-Site Scripting (XSS)
- [ ] Output encoding/escaping
- [ ] Content Security Policy
- [ ] React/Vue (auto-escapes by default)

### 8. Insecure Deserialization
- [ ] Input validation before deserialization
- [ ] Use safe serialization formats (JSON)

### 9. Using Components with Known Vulnerabilities
- [ ] Dependencies up to date
- [ ] Regular `npm audit` / `pip check`
- [ ] Automated dependency scanning

### 10. Insufficient Logging & Monitoring
- [ ] Security events logged
- [ ] Failed login attempts tracked
- [ ] Anomaly detection

## Security Headers

```typescript
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", "'unsafe-inline'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      imgSrc: ["'self'", "https:"],
    },
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
  },
}));
```

## Quick Security Wins

1. **Enable HTTPS**: Force HTTPS redirect
2. **Use Helmet.js**: Security headers middleware
3. **Rate Limiting**: Prevent brute force
4. **CORS**: Configure allowed origins
5. **Input Validation**: Validate all inputs
6. **Update Dependencies**: Run `npm audit fix`

## Security Testing

```bash
# Check for known vulnerabilities
npm audit

# Security scanning
npx snyk test

# OWASP ZAP
zap-cli quick-scan https://myapp.com
```

## Related Skills

- `validate_auth_flow` - Focused auth security
- `create_api_endpoint` - Secure API design

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luanps2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
