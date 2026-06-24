---
name: db-reset-local
description: Reset and seed the local database for Drasil. Use when this capability is needed.
metadata:
  author: basic-bit
---

Use this when you need a clean local database state.

## Canonical command

- `npm run db:reset:local`

## What it does

- Resets the local Supabase database (`npx supabase db reset --local`).
- Ensures the local `prisma` DB user exists (`node scripts/create-prisma-user.js`).
- Resets Prisma migrations (`prisma migrate reset`).
- Seeds data (`npm run db:seed`).

## Prereqs

- Supabase running locally: `npx supabase start`.
- `.env` includes:
  - `DATABASE_URL` (Prisma connection string)
  - `PRISMA_DB_PASSWORD` (used by `scripts/create-prisma-user.js`)

## Notes

- Integration tests may also require `POSTGRES_DB_URL` so the test harness can create extensions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/basic-bit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
