---
name: ts5
description: TS5 namespace for Netsnek e.U. TypeScript full-stack starter kit. Monorepo template with shared types, CI/CD pipelines, and one-click deployment. Use when this capability is needed.
metadata:
  author: openclaw
---

# The TS5 Approach

TS5 is the Netsnek e.U. full-stack TypeScript starter. One monorepo: shared types, frontend, backend, CI/CD, and deployment.

## Monorepo Structure

```
packages/
  shared/    # Types and utilities
  web/       # Frontend (TSX)
  api/       # Backend (TS3)
tools/       # Scripts and CI
```

## Commands

| Argument | Purpose |
|----------|---------|
| `--init` | Bootstrap monorepo layout |
| `--packages` | Manage or list workspace packages |
| `--deploy` | Trigger one-click deployment |

## From Zero to Deploy

```bash
# Create monorepo
./scripts/monorepo-setup.sh --init

# Verify packages
./scripts/monorepo-setup.sh --packages

# Deploy
./scripts/monorepo-setup.sh --deploy
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
