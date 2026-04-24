---
name: security-fastapi
description: |- Use when this capability is needed.
metadata:
  author: justinlevinedotme
---

<overview>

Security audit patterns for FastAPI applications covering authentication dependencies, CORS configuration, and middleware security.

</overview>

<vulnerabilities>

## Core Risks to Check

### Missing Auth on Routes

FastAPI expects authentication/authorization via dependencies on routes or routers. If no `Depends()`/`Security()` usage exists, MUST review every route for unintended public access.

```python
from fastapi import Depends, Security

@app.get("/private")
async def private_route(user=Depends(get_current_user)):
    return {"ok": True}

@app.get("/scoped")
async def scoped_route(user=Security(get_current_user, scopes=["items"])):
    return {"ok": True}
```

All sensitive routes MUST require `Depends()` or `Security()` auth dependencies.

### API Key Schemes

If using API keys, SHOULD prefer header-based schemes (`APIKeyHeader`). MUST validate the key server-side.

```python
from fastapi import Depends, FastAPI
from fastapi.security import APIKeyHeader

api_key = APIKeyHeader(name="x-api-key")

@app.get("/items")
async def read_items(key: str = Depends(api_key)):
    return {"key": key}
```

### CORS: Avoid Wildcards with Credentials

Using `allow_origins=["*"]` excludes credentialed requests (cookies/Authorization). For authenticated browser clients, MUST explicitly list allowed origins.

```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://app.example.com"],  # MUST be explicit
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

MUST NOT use `allow_origins=["*"]` with `allow_credentials=True`.

### Host Header and HTTPS Enforcement

SHOULD use Starlette middleware to prevent host-header attacks. SHOULD enforce HTTPS in production.

```python
from starlette.middleware.trustedhost import TrustedHostMiddleware
from starlette.middleware.httpsredirect import HTTPSRedirectMiddleware

app.add_middleware(TrustedHostMiddleware, allowed_hosts=["example.com", "*.example.com"])
app.add_middleware(HTTPSRedirectMiddleware)
```

</vulnerabilities>

<commands>

## Quick Audit Commands

```bash
# Detect FastAPI usage
rg -n "fastapi" pyproject.toml requirements*.txt

# Find routes
rg -n "@app\.(get|post|put|patch|delete)" . -g "*.py"

# Check for auth dependencies
rg -n "Depends\(|Security\(" . -g "*.py"

# CORS config and wildcards
rg -n "CORSMiddleware|allow_origins|allow_credentials" . -g "*.py"

# TrustedHost/HTTPS middleware
rg -n "TrustedHostMiddleware|HTTPSRedirectMiddleware" . -g "*.py"
```

</commands>

<checklist>

## Hardening Checklist

- [ ] All sensitive routes MUST require `Depends()` or `Security()` auth dependencies
- [ ] API key schemes SHOULD use headers (`APIKeyHeader`), not query params
- [ ] `allow_origins` MUST be explicit when `allow_credentials=True`
- [ ] `TrustedHostMiddleware` SHOULD be configured for production domains
- [ ] `HTTPSRedirectMiddleware` SHOULD be enabled in production (or enforced by proxy)

</checklist>

<scripts>

## Scripts

- `scripts/scan.sh` - First-pass FastAPI security scan

</scripts>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justinlevinedotme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
