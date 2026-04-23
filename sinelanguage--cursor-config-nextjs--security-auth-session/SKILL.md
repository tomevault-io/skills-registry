---
name: next-auth-session
description: Add auth/session patterns using Middleware and server-only logic. Use when this capability is needed.
metadata:
  author: sinelanguage
---
# Next.js Security, Auth, and Sessions

Implement authentication and session handling with explicit security controls.

## When to Use

- Protecting routes and APIs
- Managing sessions server-side
- Enforcing CSP and CSRF protections
- Building multi-tenant safe routing

## Inputs

- Auth provider integration
- Session storage approach (cookies vs token store)
- CSP policy requirements
- CSRF protection strategy
- Tenant identifiers and isolation rules

## Instructions

1. Keep auth logic server-only with Route Handlers or Server Actions.
2. Use Middleware for gatekeeping, redirects, and tenant resolution.
3. Validate session tokens on every protected request.
4. Set secure cookies (`httpOnly`, `secure`, `sameSite`).
5. Add CSP headers via `next.config.ts` or Middleware.
6. Enforce CSRF protection on state-changing requests.
7. Ensure tenant isolation in queries and caching boundaries.
8. Never expose secrets in Client Components.

## Output

- Auth/session flow aligned with Next.js server-first patterns.
- CSP and CSRF requirements documented.
- Multi-tenant guardrails applied to routing and data access.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sinelanguage) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
