---
name: vkc-admin-ops-workflow
description: Standardize admin operations workflow (Draft -> Review -> Scheduled publish -> Published) using DB states + admin API routes + scheduled visibility. Use when implementing admin-controlled publishing systems. (키워드= 어드민, 관리자, 발행, 예약 발행, Draft/Review/Published, 상태, 승인 워크플로우) Use when this capability is needed.
metadata:
  author: lee-sang-bok
---

# VKC Admin Ops Workflow

## When to use

- Adding any “admin-controlled publishing” feature (content drafts, policy updates, regulation updates, templates/rulesets activation)

## Canonical implementation in this repo

- Scheduled visibility model (start/end): `news` table in `src/lib/db/schema.ts`
- Admin CRUD: `src/app/api/admin/news/route.ts`
- Public read with schedule filtering: `src/app/api/news/route.ts`

## Standard workflow

- Draft → Review → Scheduled (optional) → Published
- The “published view” is derived from:
  - `isActive`
  - `startAt`/`endAt` (optional schedule window)

## What to standardize each time

- DB states and timestamps (`createdAt`, `updatedAt`, optional `startAt`, `endAt`)
- Admin endpoints (`/api/admin/**`) for CRUD + activation
- Public endpoints with schedule filtering + caching headers when appropriate

## Reference

- `.codex/skills/vkc-admin-ops-workflow/references/workflow-spec.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lee-sang-bok) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
