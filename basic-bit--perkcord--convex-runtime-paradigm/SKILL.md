---
name: convex-runtime-paradigm
description: Convex deployment/runtime decision guide for Perkcord (cloud dev vs local dev, runtime boundaries, env hygiene). Use when this capability is needed.
metadata:
  author: basic-bit
---

## Purpose

Keep Convex architecture decisions consistent in this repo and avoid flipping between incompatible models.

## Canonical model for this repo

- Hosted SaaS: Convex Cloud deployment is the source of truth for production behavior.
- Day-to-day development: use cloud dev deployment via `npx convex dev` unless explicitly testing local backend behavior.
- Local deployment mode (`npx convex dev --local`) is optional and temporary for debugging/perf; do not treat it as the default architecture.

## Runtime boundaries (must keep)

- **Queries/Mutations/HTTP actions:** default Convex runtime (deterministic for query/mutation).
- **Node runtime:** only in files with top-level `"use node"`.
- Node built-ins (`crypto`, `Buffer`, filesystem, etc.) must stay in Node runtime action files.
- Do not define queries/mutations in `"use node"` files.
- Do not import Node-only files from non-Node Convex runtime files.

## TypeScript conventions for Convex code

- Keep `convex/convex/tsconfig.json` with:
  - `"lib": ["ES2021", "dom", "dom.iterable"]`
  - `"types": ["node"]`
- Why:
  - `dom` + `dom.iterable` for Web APIs used in Convex runtime (`Request`, `Response`, `URLSearchParams.entries`, etc.).
  - `node` types for Node runtime actions using `process`, `Buffer`, and Node modules.

## Deployment/env hygiene

- Convex env vars are per deployment; set required keys in every deployment you depend on (dev/prod/preview).
- For this repo, critical Convex env vars often include:
  - `PERKCORD_REST_API_KEY`
  - `PERKCORD_OAUTH_ENCRYPTION_KEY`
  - `DISCORD_CLIENT_ID`
  - `DISCORD_CLIENT_SECRET`
  - `DISCORD_MEMBER_REDIRECT_URI`
  - `DISCORD_REDIRECT_URI`
  - `DISCORD_BOT_TOKEN`
  - Provider credentials/secrets as needed
- Convex HTTP actions should use `.convex.site` endpoints.

## When to use cloud dev vs local dev

- Use **cloud dev** when validating webhooks, OAuth callbacks, or anything needing public URLs.
- Use **local dev** only when intentionally testing local backend behavior, quotas, or latency.

## Quick verification checklist

1. `npx convex dev` links to expected project/deployment.
2. `npx tsc -p convex/convex/tsconfig.json` passes.
3. `npm --prefix convex run lint && npm --prefix convex run test` passes.
4. `npx convex env list` shows required Discord/provider env vars.
5. Role Connections metadata registration succeeds (`/api/role-connections/metadata`).

## Sharp edges

- If `npx convex env ...` returns `MissingAccessToken`, run `npx convex dev` interactively and complete login.
- If deployment mismatch prompt appears, choose the team/project deployment for this repo.
- If role sync requests stay `pending`, verify bot access to target guild and role hierarchy first.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/basic-bit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
