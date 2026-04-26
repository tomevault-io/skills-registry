---
name: auth-route-tester
description: Test authenticated HTTP routes end-to-end. Keywords: auth, test, routes. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Auth Route Tester

This workflow verifies that an authenticated route works end-to-end: correct response, correct side effects, and no obvious regressions.

---

## Purpose & Scope

Use this workflow when:
- You created or modified an authenticated route
- You need to verify a route鈥檚 functional behavior and side effects

Out of scope:
- Exhaustive negative testing (unless the task explicitly requires it)

---

## Inputs & Preconditions

Inputs:
- Route method + full URL
- Expected request payload (if any)
- Expected response shape/status
- Expected side effects (DB writes, queue messages, logs, monitoring events)

Preconditions:
- Identify authentication method (cookie/session/bearer/API key) and how to obtain test credentials safely.
- Record test inputs and assumptions in workdocs for repeatability.

---

## Steps

1. **Locate the route implementation**
   - Confirm route registration and handler wiring (route 鈫?controller 鈫?service as applicable).
2. **Construct a reproducible request**
   - Prefer a dedicated test helper under `/scripts/` or a registered ability, if available.
   - Otherwise, use a plain HTTP client (curl/Postman) with explicit headers/cookies.
3. **Run the happy-path test**
   - Confirm status code and response body shape match expectations.
4. **Verify side effects**
   - Check the authoritative data store or observable artifact (DB rows, files, emitted events).
5. **Quick sanity checks**
   - Run a minimal failure test if the task is auth-related (e.g., request without auth should be 401/403).
6. **Report**
   - Record what was tested, results, and any fixes performed.

---

## Outputs

- A short test report in workdocs (request, response, verification steps, findings)
- Optional: small fixes if testing revealed clear implementation bugs

---

## Safety Notes

- Do not hardcode secrets or real credentials in SSOT docs.
- Prefer test identities and development/test environments.

---

## Related

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
