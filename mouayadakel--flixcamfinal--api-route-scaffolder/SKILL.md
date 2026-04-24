---
name: api-route-scaffolder
description: Scaffolds API routes with validation (Zod), error handling, auth, and optional rate limiting. Use when user says "create API endpoint for [resource]" or "add CRUD routes". Use when this capability is needed.
metadata:
  author: mouayadakel
---

# API Route Scaffolder

## When to Trigger

- "Create API endpoint for [resource]"
- "Add CRUD routes"

## What to Do

Generate under app/api/[resource]/ (and [id] if needed):

- **route.ts**: GET/POST (and PATCH/DELETE for [id]) with:
  - Zod schema for body/params (aligned with Prisma model when applicable).
  - Auth check (e.g. getServerSession or project auth).
  - Rate limiting if project has it (e.g. rateLimitAPI).
  - Try/catch and appropriate status codes (400 validation, 401/403, 404, 500).
- Export handlers: GET, POST, PATCH, DELETE as appropriate.

Keep handlers thin; call services from lib/services for business logic. Add OpenAPI/docs if project uses them.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mouayadakel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
