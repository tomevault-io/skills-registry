---
name: flask-auth-security
description: Implementing professional session-based and token-based authentication. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Flask Auth & Security

Protecting user data and restricting access to critical endpoints.

## Session-based Auth (Flask-Login)
- **The UserMixin**: Simplified implementation of login/logout states.
- **decorators**: `@login_required` to protect views.

## Token-based Auth (JWT)
Use **Flask-JWT-Extended** for stateless APIs.
- **Claims**: Storing user identity and roles in the token.
- **Refresh Tokens**: Managing long-term sessions securely.

## General Security
- **Hashing**: Use `pbkdf2:sha256` or `bcrypt` for storage.
- **Headers**: Implement `HSTS`, `Content-Security-Policy`, and common security headers (using Flask-Talisman).
- **Secrets**: Never commit `SECRET_KEY` to version control.

## Best Practices
- **Rate Limiting**: Protect authentication endpoints from brute-force attacks.
- **Principle of Least Privilege**: Use Role-Based Access Control (RBAC).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
