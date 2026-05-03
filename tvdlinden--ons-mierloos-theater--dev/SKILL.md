---
name: dev
description: Start the full development environment (database, dev server, worker) Use when this capability is needed.
metadata:
  author: tvdlinden
---

Start all required services for local development of this Next.js theater ticket app.

## Steps

1. Start PostgreSQL via Docker (if not already running):

   ```bash
   docker-compose up -d db
   ```

2. Start the Next.js dev server in the background:

   ```bash
   npm run dev
   ```

3. Start the background worker (handles PDF generation, payment webhooks, orphaned order cleanup):
   ```bash
   npm run worker:watch
   ```

Steps 2 and 3 should run in separate terminals or as background processes — they are both long-running.

## Verify

- Dev server: http://localhost:3000
- Admin panel: http://localhost:3000/admin (requires login)
- Database: PostgreSQL on port 5432

If migrations haven't been applied yet, run `/migrate` first.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tvdlinden) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
