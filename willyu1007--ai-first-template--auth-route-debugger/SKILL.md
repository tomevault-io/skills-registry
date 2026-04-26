---
name: auth-route-debugger
description: Debug authenticated route issues (401/403). Keywords: auth, debug, 401, 403. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Auth Route Debugger

This workflow diagnoses authentication/authorization and route registration issues that commonly appear as 401/403/404.

---

## Purpose & Scope

Use this workflow when:
- A route returns 401/403 unexpectedly
- A route returns 404 even though it appears to exist
- Auth cookies/headers are not being accepted

Out of scope:
- Deep security redesign (requires explicit human approval)

---

## Inputs & Preconditions

Inputs:
- Route method + URL
- Observed status code and error response
- Client environment (browser vs server-to-server)

Preconditions:
- Confirm the route you are hitting is the correct service.
- Record repro steps in workdocs.

---

## Steps

1. **Confirm route registration**
   - Verify the route is mounted under the expected prefix.
   - Check ordering: a catch-all or dynamic route can shadow more specific routes.
2. **Reproduce with a minimal request**
   - Remove optional headers/body fields to isolate the failure.
3. **Test with and without auth**
   - If a 鈥渘o-auth鈥?request yields the same failure, it may not be an auth issue.
4. **Inspect auth middleware/guard**
   - Identify where identity is parsed (cookie/header/session).
   - Identify where permissions are enforced (roles/scopes/policies).
5. **Check configuration mismatches**
   - Environment-specific cookie settings (domain, secure, sameSite)
   - CORS and credentials settings (if browser client)
6. **Fix and verify**
   - Apply minimal changes.
   - Re-test with the same reproducible request.

---

## Outputs

- Root cause summary
- Minimal fix (if within scope) + verification notes
- Updated workdocs with repro steps and resolution

---

## Safety Notes

- Do not weaken auth/permission checks without explicit human approval.
- Avoid adding 鈥渢emporary bypass鈥?flags unless explicitly required and gated to non-production.

---

## Related

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
