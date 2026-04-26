---
name: route-testing-patterns
description: Patterns for testing authenticated HTTP routes. Keywords: route, testing, patterns. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Route Tester

This skill provides reusable patterns for testing authenticated HTTP routes (GET/POST/PUT/PATCH/DELETE) in a way that is repeatable, debuggable, and safe.

---

## Purpose

- Make route tests deterministic (same inputs 鈫?same observable results).
- Prevent 鈥渨orks on my machine鈥?debugging loops by standardizing how URLs, auth, and verification are handled.
- Ensure route changes are validated with both success and failure cases.

---

## When to Use

Use this skill when:
- Adding or modifying API endpoints
- Debugging 401/403 authentication and authorization issues
- Validating request/response contracts after refactors
- Verifying side effects (DB writes, background jobs, notifications)

---

## Preconditions (record these in workdocs)

Before testing, record:
- **Service base URL** (host + port)
- **Route prefix** (what the app mounts the router under)
- **Auth method** (cookie / bearer token / session / mTLS / API key)
- **Test identities** and required roles/permissions
- **Expected side effects** (tables, logs, monitoring events)

Do not hardcode environment-specific details into long-term SSOT docs.

---

## Testing methods

### Method 1: Dedicated test helper (recommended when present)

If your repository provides a helper under `/scripts/` (or an ability surfaced via `ABILITY.md`), prefer it because it can:
- Acquire tokens/cookies
- Print an equivalent `curl` command
- Standardize logging and redaction

### Method 2: Manual HTTP client (curl)

Keep commands reproducible:

```bash
curl -X POST "<BASE_URL><PREFIX>/your/route" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <TOKEN_IF_NEEDED>" \
  -b "<COOKIE_NAME>=<COOKIE_VALUE_IF_NEEDED>" \
  -d '{"example":"payload"}'
```

### Method 3: Development-only mock auth (if supported)

If the service supports dev-only mock authentication:
- Ensure it is strictly gated to `development`/`test` environments.
- Document the required headers/env vars in **workdocs**, not SSOT.

---

## Checklist

1. Identify the correct service and base URL
2. Confirm route prefix and construct the full URL
3. Prepare request body and headers
4. Acquire valid auth (or enable dev-only mock auth)
5. Test the success path
6. Test key failure paths:
   - invalid input (400)
   - missing/invalid auth (401)
   - insufficient permissions (403)
   - missing resource (404)
7. Verify side effects:
   - DB rows changed
   - monitoring events captured
   - logs show expected correlation/request ids

---

## Debugging guide (common failures)

### 401 Unauthorized

Common causes:
- Missing or malformed auth
- Token expired or issued for the wrong audience/issuer
- Cookie domain/path mismatch

Actions:
- Re-acquire auth
- Print and compare the raw request (headers/cookies)
- Check server logs for auth middleware errors

### 403 Forbidden

Common causes:
- Identity lacks required role/permission
- Route guard configured incorrectly

Actions:
- Verify required permissions and user roles
- Add a minimal reproduction case and record it in workdocs

### 404 Not Found

Common causes:
- Wrong prefix or route mount point
- Service is not the one you think it is

Actions:
- Check the route registration code (router mount)
- Confirm the request hits the intended service

### 500 Internal Server Error

Actions:
- Check structured logs and monitoring traces
- Reduce the request to a minimal payload
- Verify DB connectivity and configuration

---

## Related

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
