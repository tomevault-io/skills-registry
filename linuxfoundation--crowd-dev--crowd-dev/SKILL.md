---
name: packages-worker-add-entrypoint
description: > Use when this capability is needed.
metadata:
  author: linuxfoundation
---

# packages-worker — Add a New Sub-worker

You are adding a new data-ingestion worker to `services/apps/packages_worker/`.
The structure follows the same pattern as `backend/` (where `api.ts` and
`job-generator.ts` share one Dockerfile): one npm package, one Docker image,
each worker in its own `src/{worker}/` directory with its own entry point.

```
services/apps/packages_worker/
  src/
    bin/
      github-repos-enricher.ts  ← existing worker
      <name>.ts                 ← entry point you will create
    github/                     ← existing worker logic
    <worker>/                   ← directory you will create
      index.ts                  ← main logic for this worker
      types.ts
    config.ts                   ← shared — add your config getter here
    db.ts                       ← shared — do not modify
```

## Step 1 — Gather requirements

Ask the engineer for:

1. **Worker name** (kebab-case) — e.g. `npm-sync`, `osv-sync`, `scorecard-runner`. Used as the entry point filename (`src/bin/<name>.ts`) and docker-compose service name.
2. **Worker directory name** (short, lowercase) — e.g. `npm`, `osv`, `scorecard`. Becomes `src/<worker>/`.
3. **What it does** — what data it fetches/writes, what table(s) in packages-db it reads from and writes to.
4. **External API or data source** (if any) — URL, auth method, rate-limit characteristics.
5. **Required env vars** beyond the shared DB vars — e.g. `NPM_API_URL`, `OSV_API_KEY`.

Do not proceed until you have answers to 1–3.

## Step 2 — Read existing files first

```bash
cat services/apps/packages_worker/src/bin/github-repos-enricher.ts
cat services/apps/packages_worker/src/config.ts
cat services/apps/packages_worker/package.json
cat scripts/services/github-repos-enricher.yaml
```

These are the canonical references. Do not deviate from the patterns you see there.

## Step 3 — Scaffold the files

### 3a. Worker directory — `services/apps/packages_worker/src/<worker>/`

Create the directory with at minimum:

**`types.ts`** — types specific to this worker (input/output shapes, error kinds if calling an external API).

**`index.ts`** — the main logic function(s) this worker runs. What goes here depends entirely on what the worker does — do not force a loop shape if it does not fit. Discuss with the engineer what the execution model should be (continuous loop, one-shot batch, event-driven, etc.) and implement accordingly.

Add any additional files the worker needs (e.g. an API client, a DB query helper). All DB access uses inline pg-promise SQL via `qx.select` / `qx.result` / `qx.none` — do not add files to `services/libs/data-access-layer`.

### 3b. Entry point — `services/apps/packages_worker/src/bin/<name>.ts`

Follow the structure of `github-repos-enricher.ts`:
- Import `getServiceLogger` from `@crowd/logging`
- Import your worker's config getter from `../config` and `getPackagesDb` from `../db`
- Import your worker's main function from `../<worker>/index`
- Set `liveFilePath` / `readyFilePath` to `../tmp/<name>-live.tmp` / `../tmp/<name>-ready.tmp`
- Handle SIGINT / SIGTERM with a `shuttingDown` flag
- In `main()`: call config getter → validate any required tokens/keys → `await getPackagesDb()` → `await qx.selectOne('SELECT 1')` → `fs.mkdirSync` for the tmp dir → `setInterval` writing probe files every 5000ms → call your worker's main function → `clearInterval` → `process.exit(0)`
- Fatal handler: `main().catch(err => { log.error({ err }, '<name> fatal error'); process.exit(1) })`

### 3c. Config additions — `services/apps/packages_worker/src/config.ts`

Read the file first, then add a `get<Worker>Config()` function:
- Use `requireEnv(name)` for string vars, `requireEnvInt(name)` for integers
- No defaults, no `?? undefined` — the process must refuse to start on missing config

### 3d. Docker-compose service — `scripts/services/<name>.yaml`

Copy `scripts/services/github-repos-enricher.yaml` and adapt:
- Service names: `<name>` (prod) and `<name>-dev` (dev)
- `command` (prod): `pnpm run start:<name>`
- `command` (dev): `pnpm run dev:<name>`
- `env_file`: keep the same four files (`backend/.env.dist.local`, `backend/.env.dist.composed`, `backend/.env.override.local`, `backend/.env.override.composed`)
- `environment`: set any tuning var defaults inline (avoids requiring them in `.env.override.local` for local dev)
- `volumes` (dev only): bind-mount `./services/apps/packages_worker/src` plus every `services/libs/*/src` directory (copy the full list from the enricher yaml for hot reload)

### 3e. package.json scripts — `services/apps/packages_worker/package.json`

Read the file first, then add:
```json
"start:<name>": "tsx src/bin/<name>.ts",
"dev:<name>": "tsx watch src/bin/<name>.ts"
```

### 3f. Env var files — `backend/.env.dist.local` and `backend/.env.dist.composed`

Append new required vars with empty-string defaults (or sensible local values for non-secrets):
```
NEW_WORKER_API_KEY=
```

## Step 4 — TypeScript check

```bash
cd services/apps/packages_worker && pnpm tsc --noEmit
```

Fix any errors before proceeding.

## Checklist before committing

- [ ] `src/<worker>/` directory created with `types.ts` and `index.ts`
- [ ] `src/bin/<name>.ts` — probe files, SIGINT/SIGTERM handler, fail-fast config check, `SELECT 1` on startup
- [ ] `config.ts` — new `get<Worker>Config()` using `requireEnv`/`requireEnvInt`, no defaults
- [ ] `scripts/services/<name>.yaml` — prod + dev services with bind mounts
- [ ] `package.json` — `start:<name>` and `dev:<name>` scripts added
- [ ] `backend/.env.dist.local` and `.env.dist.composed` — new vars documented
- [ ] No new files in `services/libs/data-access-layer` (packages-db uses inline SQL)
- [ ] `pnpm tsc --noEmit` passes

Use `/preflight` before opening a PR and `/commit` to sign off.

---
> Source: [linuxfoundation/crowd.dev](https://github.com/linuxfoundation/crowd.dev) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-27 -->
