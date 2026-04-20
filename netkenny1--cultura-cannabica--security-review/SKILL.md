---
name: security-review
description: Use when adding auth, handling user input, working with secrets, creating API endpoints, or implementing payment/sensitive features. Provides security checklist and patterns.
metadata:
  author: netkenny1
---

# Security Review Skill

Use this skill when implementing authentication, handling user input, working with secrets, creating API endpoints, or implementing payment/sensitive features.

## When to Activate

- Implementing authentication or authorization
- Handling user input or file uploads
- Creating new API endpoints
- Working with secrets or credentials
- Implementing payment or checkout flows
- Storing or transmitting sensitive data

## Security Checklist

### 1. Secrets Management

- **Never** hardcode API keys, passwords, or tokens in source
- **Always** use environment variables; validate required vars at startup
- Keep `.env` and `.env.local` in .gitignore; no secrets in git history
- Production secrets in hosting platform (Vercel, Railway, etc.)

### 2. Input Validation

- Validate all user input at boundaries (query, body, headers)
- Use schema-based validation (e.g. Zod) where available
- File uploads: restrict size, type, and extension; whitelist only
- Error messages must not leak sensitive data or internal details

### 3. Injection Prevention

- **Never** concatenate user input into SQL; use parameterized queries or safe ORMs
- **Never** inject user input into HTML without escaping; use safe APIs or templating
- Sanitize or escape before rendering user content (XSS)

### 4. Auth and Authorization

- Protect sensitive routes (checkout, account, admin) with session or token
- Verify permissions before performing actions; return 403 when insufficient
- Use secure cookies (httpOnly, secure, sameSite) for sessions

### 5. Rate Limiting and Headers

- Rate limit all public endpoints (per IP or per user)
- Set security headers: X-Content-Type-Options, X-Frame-Options, CSP, etc.
- Use HTTPS in production; HSTS where supported

### 6. Error Handling

- Do not expose stack traces or internal paths to clients
- Log errors server-side with context; return generic messages to users
- Rotate any secrets that may have been exposed

## Response Protocol

If a security issue is found:

1. Stop and fix critical issues before continuing
2. Rotate any exposed secrets
3. Review the codebase for similar patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/netkenny1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
