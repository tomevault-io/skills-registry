---
name: infra
description: > Use when this capability is needed.
metadata:
  author: dtaborda
---

## When to Use

- Building the `infra/` folder (Dockerfiles, compose, env templates)
- Updating how frontend ↔ backend communicate in containerized environments
- Adding scripts for local Docker workflows or CI pipelines tied to infra
- Auditing existing Docker settings to keep them aligned with `docs/architecture.md`

## Critical Patterns

### Folder Layout

```
infra/
├── docker-compose.yml
├── frontend.Dockerfile
├── backend.Dockerfile
└── README.md (explain ports, env, volume usage)
```

- Keep infra-specific assets isolated from app packages
- Compose file must live under `infra/` and reference Dockerfiles via relative paths (e.g., `build: { context: .., dockerfile: infra/frontend.Dockerfile }`)

### Service Contracts

| Service | Port (container → host) | Notes |
|---------|------------------------|-------|
| `frontend` | 3000:3000 | Runs `next start`; use env `NEXT_PUBLIC_API_BASE_URL=http://backend:4000` or Compose network alias |
| `backend` | 4000:4000 | Runs Express build output (`node dist/app.js`) |

- No database or queue services for this challenge. Reject attempts to introduce them.
- Use Docker network defaults so `frontend` can reach `http://backend:4000` internally; do **not** expose backend publicly unless required.

### Dockerfile Expectations

Frontend:

1. Multi-stage build (`builder`, `runner`)
2. Base image: `node:20-alpine` (matches engines)
3. Copy monorepo files, install pnpm globally via corepack (or `pnpm fetch`), then `pnpm --filter frontend...`
4. Output: `.next/`, `node_modules` for production only (use `next build && next start`)
5. Set `PORT=3000`, `HOST=0.0.0.0`

Backend:

1. Multi-stage (`builder` compiles TypeScript, `runner` copies `dist/`)
2. Use `pnpm fetch` + `pnpm install --offline` to leverage the lockfile
3. Expose `PORT=4000`
4. Entrypoint: `node dist/app.js`

Shared package should be built in the builder stage via `pnpm --filter shared build` before frontend/backend builds.

### Compose Guardrails

- Always mount `.env.example` → `.env` with explicit `env_file:` entries; never rely on host envs implicitly
- Use `depends_on` so backend is ready before frontend tries to proxy API calls
- Provide a `volumes:` override only for dev (e.g., `.:/app` + `pnpm install`), but default compose stack should run the built artifacts
- Document commands inside `infra/README.md`

### Networking & Rewrites

- Next.js should continue to proxy `/api` to `backend:4000`; do **not** open CORS on backend unless Compose rewrites prove impossible
- For local Docker usage, prefer hitting `http://localhost:3000` and let rewrites map to backend automatically

### ALWAYS

- Build shared artifacts in dependency order (shared → backend/frontend) inside Dockerfiles
- Copy only necessary files into runtime layers (no `.git`, no local dev config)
- Keep Compose deterministic: no `latest` tags, always build from the local source
- Mirror the same Node/pnpm versions defined in `package.json` engines

### NEVER

- Add databases, queues, or extra services unless the spec explicitly changes
- Run `pnpm install` twice (use `pnpm fetch` + `pnpm install --offline` once per stage)
- Use root user in production layers; switch to `node` user when possible
- Bake secrets or `.env` values directly into images—pass through env vars at runtime

## Commands

```bash
# Build & run the stack locally (from repo root)

# Tear down containers and remove orphaned services

# Rebuild a single service
docker compose -f infra/docker-compose.yml build backend

# Follow logs

# Clean dangling images created during local iterations
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dtaborda) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
