---
name: api-route-safety
description: Enforce authentication, validation, idempotency, and multi-tenant scoping for app/api routes. Use when this capability is needed.
metadata:
  author: jelidia
---

Checklist:
- Require auth (`requireUser`/`requireRole`/`requirePermission`) in `app/api/**/route.ts`.
- Scope DB queries by `company_id` (multi-tenant).
- Request validation schemas come from `lib/validators.ts` only.
- Retryable writes use `lib/idempotency.ts`.
- Sensitive actions log via `lib/auditLog.ts`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jelidia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
