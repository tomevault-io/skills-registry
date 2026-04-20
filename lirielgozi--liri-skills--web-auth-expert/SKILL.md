---
name: web-auth-expert
description: | Use when this capability is needed.
metadata:
  author: lirielgozi
---

# Web Authentication Implementation Expert

Implement production-ready authentication for web applications.

## Stack
- **Frontend**: TypeScript (React, Next.js, Vue)
- **Backend**: Python (FastAPI, Flask, Django)
- **Standards**: OAuth 2.1 (draft), OIDC, WebAuthn, TOTP RFC 6238, OWASP ASVS 5.0

## Orchestrator

This skill dispatches to specialized sub-agents based on the task:

| Sub-Agent | Purpose | Dispatch When |
|-----------|---------|---------------|
| `oauth-implementer` | OAuth 2.1, OIDC, PKCE, social login | User needs OAuth/social login |
| `passkey-implementer` | WebAuthn, FIDO2, passkeys | User needs passkeys/passwordless |
| `mfa-implementer` | TOTP, hardware keys, step-up auth | User needs MFA |
| `security-hardener` | CSRF, XSS, headers, rate limiting | User needs security hardening |
| `compliance-checker` | CCSS, SOC 2, GDPR, ASVS verification | User mentions compliance |

**Context Efficiency**: Load only the relevant sub-agent's references to minimize context usage.

## Quick Method Selection

| App Type | Primary Auth | MFA | Session |
|----------|--------------|-----|---------|
| Consumer SaaS | Social login (Google/Apple) | Optional TOTP | JWT in HttpOnly cookie |
| Enterprise B2B | OIDC SSO | Required (TOTP/hardware key) | Server-side sessions |
| Crypto/Financial | Passkeys + password fallback | Required + step-up auth | Short-lived JWT + refresh |
| Internal tools | Corporate SSO | Policy-based | Server-side sessions |

## Implementation Workflow

1. **Determine requirements** → Read `references/decision-framework.md`
2. **Implement primary auth method** → Read appropriate `references/methods/` file
3. **Add social providers if needed** → Read `references/social-providers/`
4. **Implement session management** → Read `references/session-management/`
5. **Add MFA if required** → Read `references/mfa/`
6. **Apply security hardening** → Read `references/security/`
7. **Verify compliance** → Read `references/compliance/` if applicable

## Core Patterns (Quick Reference)

### OAuth 2.1 Authorization Code + PKCE

**Frontend (TypeScript):**
```typescript
// Generate PKCE pair
function generatePKCE(): { verifier: string; challenge: string } {
  const verifier = crypto.randomUUID() + crypto.randomUUID();
  const encoder = new TextEncoder();
  const data = encoder.encode(verifier);
  const hash = await crypto.subtle.digest('SHA-256', data);
  const challenge = btoa(String.fromCharCode(...new Uint8Array(hash)))
    .replace(/\+/g, '-').replace(/\//g, '_').replace(/=+$/, '');
  return { verifier, challenge };
}

// Store verifier in sessionStorage, send challenge to /authorize
sessionStorage.setItem('pkce_verifier', pkce.verifier);
```

**Backend (Python/FastAPI):**
```python
from authlib.integrations.starlette_client import OAuth
from fastapi import FastAPI, Request
from fastapi.responses import RedirectResponse

oauth = OAuth()
oauth.register(
    name="google",
    client_id=settings.GOOGLE_CLIENT_ID,
    client_secret=settings.GOOGLE_CLIENT_SECRET,
    server_metadata_url="https://accounts.google.com/.well-known/openid-configuration",
    client_kwargs={"scope": "openid email profile"},
)

@app.get("/auth/google")
async def google_login(request: Request):
    redirect_uri = request.url_for("google_callback")
    return await oauth.google.authorize_redirect(request, redirect_uri)

@app.get("/auth/google/callback")
async def google_callback(request: Request):
    token = await oauth.google.authorize_access_token(request)
    user_info = token.get("userinfo")
    # Create session, return JWT
```

### Password Hashing (Argon2id)

```python
from argon2 import PasswordHasher
from argon2.exceptions import VerifyMismatchError

ph = PasswordHasher(
    time_cost=2,        # iterations
    memory_cost=19456,  # 19 MiB
    parallelism=1,
    hash_len=32,
    salt_len=16
)

def hash_password(password: str) -> str:
    return ph.hash(password)

def verify_password(hash: str, password: str) -> bool:
    try:
        ph.verify(hash, password)
        return True
    except VerifyMismatchError:
        return False
```

### JWT Session Cookie

```python
from datetime import datetime, timedelta
import jwt

def create_session_token(user_id: str, secret: str) -> str:
    payload = {
        "sub": user_id,
        "iat": datetime.utcnow(),
        "exp": datetime.utcnow() + timedelta(hours=1),
    }
    return jwt.encode(payload, secret, algorithm="HS256")

def set_session_cookie(response, token: str):
    response.set_cookie(
        key="session",
        value=token,
        httponly=True,
        secure=True,
        samesite="lax",
        max_age=3600,
    )
```

### TOTP MFA

```python
import pyotp

def generate_totp_secret() -> str:
    return pyotp.random_base32()

def get_totp_uri(secret: str, email: str, issuer: str) -> str:
    return pyotp.totp.TOTP(secret).provisioning_uri(
        name=email, issuer_name=issuer
    )

def verify_totp(secret: str, code: str) -> bool:
    totp = pyotp.TOTP(secret)
    return totp.verify(code, valid_window=1)  # Allow 30s drift
```

## References

### Methods
- `references/methods/oauth-oidc-pkce.md` - OAuth 2.1, OIDC, PKCE implementation
- `references/methods/passkeys-webauthn.md` - FIDO2/WebAuthn/Passkeys
- `references/methods/magic-links.md` - Email passwordless
- `references/methods/password-auth.md` - Traditional auth + hashing

### Social Providers
- `references/social-providers/overview.md` - Common patterns, account linking
- `references/social-providers/google.md` - Google Sign-In
- `references/social-providers/apple.md` - Sign in with Apple
- `references/social-providers/facebook.md` - Facebook Login
- `references/social-providers/github.md` - GitHub OAuth

### MFA
- `references/mfa/overview.md` - Method comparison
- `references/mfa/totp.md` - TOTP implementation
- `references/mfa/hardware-keys.md` - YubiKey, FIDO2 keys
- `references/mfa/step-up-auth.md` - Re-auth for sensitive operations

### Session Management
- `references/session-management/jwt-vs-cookies.md` - When to use which
- `references/session-management/token-storage.md` - Secure storage (TypeScript)
- `references/session-management/session-security.md` - Timeouts, rotation

### Security
- `references/security/password-hashing.md` - Argon2id, bcrypt, migration
- `references/security/csrf-xss-protection.md` - Attack prevention
- `references/security/rate-limiting.md` - Brute force protection

### Compliance
- `references/compliance/ccss.md` - Crypto app requirements
- `references/compliance/soc2.md` - SOC 2 auth requirements
- `references/compliance/gdpr-data.md` - GDPR considerations

## Sub-Agents
- `sub_agents/oauth-implementer.md` - OAuth 2.1, OIDC, PKCE flows
- `sub_agents/passkey-implementer.md` - WebAuthn/FIDO2/Passkeys
- `sub_agents/mfa-implementer.md` - TOTP, hardware keys, step-up auth
- `sub_agents/security-hardener.md` - CSRF, XSS, headers, rate limiting
- `sub_agents/compliance-checker.md` - CCSS, SOC 2, GDPR verification

## Workflows
- `workflows/new-crypto-app.md` - Crypto dashboard auth setup
- `workflows/new-standard-app.md` - Standard web app auth
- `workflows/add-social-login.md` - Adding OAuth providers
- `workflows/add-mfa.md` - Implementing MFA
- `workflows/security-audit.md` - Auth security review

## Orchestrator Agent

This skill has an associated orchestrator agent at `.claude/agents/web-auth-expert.md` that coordinates the sub-agents and workflows. The orchestrator:

- Identifies the authentication domain (OAuth, passkeys, MFA, security, compliance)
- Dispatches to appropriate sub-agents
- Coordinates multi-agent tasks for complex requests
- Provides code for both TypeScript frontend and Python backend

## Research
- `references/RESEARCH.md` - Latest auth best practices (January 2025)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lirielgozi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
