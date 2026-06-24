---
name: wtc
description: Use when user invokes /wtc to work with Docker Compose worktree isolation, start/stop infra per worktree, or check worktree status
metadata:
  author: 0-sayed
---

# wtc — worktree-compose

Zero-config Docker Compose isolation for git worktrees. Each worktree gets its own containers, ports, and volumes — no conflicts when running multiple branches simultaneously.

**Repo:** https://github.com/LevwTech/worktree-compose

---

## Port Formula

```
remapped_port = 20000 + default_port + worktree_index
```

Worktree index comes from position in `git worktree list`. Run `wtc ls` to see current assignments.

---

## Port Collision Problem

The default formula `20000 + port + index` creates collisions when services have consecutive ports:

```
Service ports: 8001, 8002, 8003, 8004

WT1: 28002, 28003, 28004, 28005
WT2: 28003, 28004, 28005, 28006  ← overlaps WT1
WT3: 28004, 28005, 28006, 28007  ← overlaps WT1 & WT2
```

**Fix:** Use `index * 100` offset instead of just `index`:

```
WT1: 28101, 28102, 28103, 28104
WT2: 28201, 28202, 28203, 28204  ← no overlap
WT3: 28301, 28302, 28303, 28304  ← no overlap
```

After `wtc start`, run the fix script inside the worktree:

```bash
cd <worktree-path>
bash "${HOME}/.agents/skills/wtc/scripts/wtc-fix-ports"
```

The script auto-detects the worktree index from known infra ports (postgres, redis, etc.) and fixes ALL `*_PORT` variables in `.env`.

---

## Database Readiness

`wtc` isolates Docker infra. It does NOT guarantee database state.

**After `wtc start`, before starting your app:**

1. Verify database is reachable on the remapped port
2. Run migrations if needed
3. Run seeds if needed

Do NOT assume a running database means migrations are applied or seed data exists.

---

## Commands

**CRITICAL: Always run wtc from the repo root**, not from inside a worktree.

```bash
wtc ls                  # list all worktrees with indices, status, ports
wtc start 1 3           # start infra for worktrees by index
wtc stop 1 3            # stop (preserves volumes)
wtc restart 1           # stop → resync → rebuild → start
wtc promote 1           # copy worktree changes into current branch
wtc clean               # stop all, remove all worktrees + prune Docker (DESTRUCTIVE)
```

---

## Workflow

```bash
# 1. From repo root
cd /path/to/repo
wtc ls                    # see worktree indices
wtc start <index>         # start Docker infra

# 2. Move into worktree
cd <worktree-path>

# 3. Fix port collisions (if project has consecutive service ports)
bash "${HOME}/.agents/skills/wtc/scripts/wtc-fix-ports"

# 4. Check database readiness
# Run migrations/seeds if needed

# 5. Start app (project-specific)
# e.g., make dev, pnpm dev, npm run dev
```

---

## What wtc Does

1. Creates isolated Docker Compose project per worktree
2. Remaps ports so each worktree has unique ports
3. Syncs docker-compose.yml and .env from main branch
4. Injects port overrides into worktree's .env

---

## What wtc Does NOT Do

- Start your app servers (backend, frontend)
- Run database migrations
- Seed data
- Manage browser automation
- Fix port collisions automatically (you need to patch .env)

These are project-specific — handle them via Makefile, scripts, or the repo's instructions file.

---

## Port Requirements

wtc can only remap ports declared with env var defaults:

```yaml
# wtc can remap
ports:
  - "${POSTGRES_PORT:-5432}:5432"

# wtc skips (hardcoded)
ports:
  - "5432:5432"
```

---

## Gotchas

- **Run from repo root** — wtc derives project name from current directory
- **wtc syncs from main** — worktree-local docker-compose.yml changes are overwritten
- **--build flag** — `wtc start` runs `docker compose up -d --build`, which checks network for image updates. For fast startup after first run, use `wtc-up` script (see below).
- **Consecutive ports collide** — see Port Collision Problem above

---

## Optional: wtc as MCP Server

```json
{
  "mcpServers": {
    "wtc": {
      "command": "npx",
      "args": ["wtc", "mcp"]
    }
  }
}
```

---

## Optional: .wtcrc.json

```json
{
  "sync": [".generated/prisma-client"],
  "envOverrides": {
    "VITE_API_URL": "http://localhost:${BACKEND_PORT}"
  }
}
```

---

## Scripts Bundled with This Skill

| Script | Purpose | When to use |
|--------|---------|-------------|
| `wtc-fix-ports` | Fix port collisions | After `wtc start`, if project has consecutive service ports |
| `wtc-up` | Fast startup (no --build) | Daily startup when images already exist |

### wtc-up (Fast Startup)

`wtc start` always uses `--build` which checks network for base image updates. Use `wtc-up` for fast startup:

```bash
cd <worktree-path>
bash "${HOME}/.agents/skills/wtc/scripts/wtc-up"
```

**When to use which:**
- `wtc start <index>` — First time setup, or after Dockerfile changes
- `wtc-up` — Daily startup, images already built, no network checks

---

## Additional Resources

See [DECISIONS.md](DECISIONS.md) for the reasoning behind design decisions in this skill.

---
> Source: [0-sayed/auto-pr](https://github.com/0-sayed/auto-pr) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
