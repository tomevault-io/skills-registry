---
name: authentication
description: Guidelines for auth flows, session management, OAuth, JWT, and access control patterns Use when this capability is needed.
metadata:
  author: xmenq
---

# Authentication Skill

## When to use this skill

Use when implementing login/signup, session management, OAuth integrations, role-based access control, or any security-sensitive auth feature.

---

## Auth Principles

### 1. Never roll your own crypto
- Use established libraries for hashing, tokens, encryption
- Don't invent custom auth protocols
- Follow OWASP guidelines

### 2. Defense in depth
- Auth at the API gateway / middleware (not per-endpoint)
- Validate tokens on every request
- Never trust the client

### 3. Least privilege
- Users get minimum permissions needed
- Tokens scope to specific actions
- Admin access is audited

---

## Authentication Patterns

### Pattern 1: Session-Based Auth
```
Client → POST /login (credentials) → Server creates session
Client → requests with session cookie → Server validates session
Client → POST /logout → Server destroys session
```

**Best for:** Traditional web apps, server-rendered pages

**Rules:**
- Store sessions server-side (database or Redis), not in cookies
- Use HTTP-only, Secure, SameSite cookies
- Set session expiry (24h default, configurable)
- Regenerate session ID after login (prevent session fixation)
- Invalidate session on logout and password change

### Pattern 2: JWT (JSON Web Token)
```
Client → POST /login (credentials) → Server returns access + refresh tokens
Client → requests with Authorization: Bearer <access_token>
Client → POST /refresh (refresh_token) → Server returns new access token
```

**Best for:** APIs, SPAs, mobile apps, microservices

**Rules:**
- **Access tokens**: short-lived (15-30 minutes)
- **Refresh tokens**: longer-lived (7-30 days), stored securely
- Never store JWTs in localStorage (use HTTP-only cookies or memory)
- Include minimal claims (user ID, role — never sensitive data)
- Validate signature, expiry, and issuer on every request
- Support token revocation (blacklist or short-lived + rotation)

### Pattern 3: OAuth 2.0 / OpenID Connect
```
Client → Redirect to provider → User authenticates → Redirect back with code
Server → Exchange code for tokens → Create local session/JWT
```

**Best for:** "Login with Google/GitHub/etc.", third-party integrations

**Rules:**
- Use Authorization Code Flow with PKCE (not Implicit Flow)
- Validate `state` parameter to prevent CSRF
- Validate ID token claims (issuer, audience, expiry)
- Store provider tokens securely if you need to call their APIs

---

## Password Handling

### Rules
- **Hash passwords with bcrypt, scrypt, or Argon2** — never MD5/SHA
- **Salt automatically** (bcrypt does this) — never reuse salts
- **Minimum password length: 8 characters** — prefer 12+
- **Check against breached password lists** (Have I Been Pwned API)
- **Never log passwords** — not even hashed ones
- **Never send passwords in URLs** — always POST body
- **Never store plaintext passwords** — anywhere, ever

### Password reset flow
1. User requests reset → generate random token (not the password hash)
2. Send token via email → link expires in 1 hour
3. User clicks link → verify token, show new password form
4. User sets new password → invalidate token, invalidate all sessions
5. Confirm via email → notify user of password change

---

## Authorization (Access Control)

### Role-Based Access Control (RBAC)
```
User → has Role(s) → Role has Permission(s) → Permission grants Action on Resource
```

| Role | Permissions |
|------|------------|
| `viewer` | Read resources |
| `editor` | Read + create + update resources |
| `admin` | All operations + user management |
| `owner` | All operations + billing + delete |

### Authorization rules
- **Check permissions on every request** — middleware, not ad-hoc
- **Deny by default** — if no permission is found, deny access
- **Resource-level checks** — user can edit *their* data, not *all* data
- **Audit sensitive actions** — log who did what, when

### Authorization checklist per endpoint
```
1. Is the user authenticated? (401 if not)
2. Does their role include this permission? (403 if not)
3. Do they own/have access to this specific resource? (404 if not)
```

> Return 404 (not 403) for resources the user shouldn't know exist.

---

## Security Hardening

### Rate limiting
- Login: 5 attempts per minute per IP/account
- Password reset: 3 requests per hour per email
- API: per-key limits based on plan

### Account lockout
- Lock after 10 failed login attempts
- Require email verification to unlock
- Notify user of suspicious activity

### Multi-factor authentication (MFA)
- Offer TOTP (authenticator app) as second factor
- Support recovery codes for lost authenticator
- Enforce MFA for admin/sensitive roles

### Session security
- Regenerate session ID after privilege changes
- Invalidate all sessions on password change
- Show active sessions to users (allow revocation)
- Set reasonable idle timeout (30 min for sensitive apps)

---

## Token Storage Guide

| Storage method | Security | Use case |
|---------------|----------|----------|
| HTTP-only Secure cookie | ✅ Best for web | Sessions, access tokens |
| In-memory (JS variable) | ✅ Good for SPAs | Short-lived access tokens |
| Secure OS keychain | ✅ Best for mobile | Refresh tokens |
| localStorage | ❌ Avoid | Vulnerable to XSS |
| URL parameters | ❌ Never | Logged, cached, leaked |

---

## PR Checklist for Auth Changes

- [ ] Uses established auth library (no custom crypto)
- [ ] Passwords hashed with bcrypt/scrypt/Argon2
- [ ] Tokens are properly scoped and expire
- [ ] Access control checked at middleware level
- [ ] No sensitive data in logs, URLs, or error messages
- [ ] Rate limiting on auth endpoints
- [ ] Session invalidation on logout/password change
- [ ] Tests cover: valid login, invalid credentials, expired token, insufficient permissions
- [ ] Security review flagged for this PR

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xmenq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
