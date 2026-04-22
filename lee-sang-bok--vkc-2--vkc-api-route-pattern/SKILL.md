---
name: vkc-api-route-pattern
description: Standardize Next.js App Router API route implementations under src/app/api/** (auth/session, input validation, Drizzle queries, rate limiting, response shape). Use when creating or refactoring API routes in this repo. (키워드= API 라우트, route.ts, 세션/권한, 입력 검증, Zod, validateBody, Drizzle, 레이트리밋) Use when this capability is needed.
metadata:
  author: lee-sang-bok
---

# VKC API Route Pattern

## When to use

- Creating new endpoints under `src/app/api/**`
- Refactoring existing endpoints to match house style

## House style (this repo)

- **Auth**: `getSession` (user) or `getAdminSession` (admin)
- **DB**: `db` from `@/lib/db`, tables from `@/lib/db/schema`
- **Rate limit** (if needed): `checkRateLimit` + `rateLimitResponse`
- **Responses**:
  - Public APIs: prefer `@/lib/api/response` helpers
  - Admin APIs: typically `NextResponse.json(...)` directly (keep consistent within `/api/admin/**`)
- **Validation**: this repo uses **Zod**. For new/refactor APIs, prefer `@/lib/api/validation` (`src/lib/api/validation.ts`) helpers like `validateBody` + `createJsonBodyReader`.

## Canonical references

- Public route w/ validation + rate-limit: `src/app/api/reports/route.ts`
- Public route w/ typed allowlists: `src/app/api/events/route.ts`
- Admin CRUD + schedule fields: `src/app/api/admin/news/route.ts`
- Validation helpers: `src/lib/api/validation.ts`

## Template

- Full skeleton (copy + customize): `.codex/skills/vkc-api-route-pattern/references/api-route-template.ts`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lee-sang-bok) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
