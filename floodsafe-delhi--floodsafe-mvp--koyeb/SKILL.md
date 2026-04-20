---
name: koyeb
description: Manage Koyeb backend services - check status, view logs, redeploy, list deployments Use when this capability is needed.
metadata:
  author: floodsafe-delhi
---

# Koyeb Service Management

Manage FloodSafe backend services on Koyeb using the CLI.

**CLI Path**: `./koyeb-cli-extracted/koyeb.exe`

## Commands

Based on `$ARGUMENTS`, run the appropriate command:

### `status` or `services` (default if no argument)
```bash
./koyeb-cli-extracted/koyeb.exe services list
```

### `logs [service-name]`
Query recent logs. Default service: `floodsafe-backend/backend`
```bash
./koyeb-cli-extracted/koyeb.exe logs floodsafe-backend
```

### `redeploy [service-name]`
Redeploy a service. Default: `floodsafe-backend/backend`
```bash
./koyeb-cli-extracted/koyeb.exe services redeploy floodsafe-backend/backend
```
**⚠️ Ask for confirmation before running redeploy.**

### `deployments`
List recent deployments:
```bash
./koyeb-cli-extracted/koyeb.exe deployments list
```

### `get [service-name]`
Get detailed info about a service:
```bash
./koyeb-cli-extracted/koyeb.exe services get floodsafe-backend/backend
```

## Environment
The CLI reads `KOYEB_TOKEN` from the environment. If commands fail with auth errors, remind the user to set the token:
```bash
set KOYEB_TOKEN=<token>
```

## FloodSafe Services
| Service | Koyeb Name |
|---------|-----------|
| Backend API | `floodsafe-backend/backend` |
| ML Service | `floodsafe-ml/ml-service` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/floodsafe-delhi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
