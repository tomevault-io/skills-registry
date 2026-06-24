---
name: tilt-up
description: Start ImmorTerm development environment with Tilt. Use this skill to start local dev services (landing page, API). ALWAYS use this instead of running `tilt up` directly. Use when this capability is needed.
metadata:
  author: lonormaly
---

# Tilt Up - Start ImmorTerm Dev Environment

**CRITICAL**: NEVER run `tilt up` directly! Always use the startup script. Multiple Tilt projects run in parallel (ImmorTerm port 10390, Pax port 10360/10361, etc.).

## Usage

```bash
./scripts/tilt_up.sh [options]
```

## Options

| Flag | Description |
|------|-------------|
| `--port PORT` | Override default port (default: 10390) |
| `--services api,web,...` | Only start specific services |

## Services

| Service | URL | Description | Auto-start |
|---------|-----|-------------|------------|
| web | http://web.immorterm.localhost:1355 | Next.js landing page | Yes |
| api | http://api.immorterm.localhost:1355 | Hono API server | Yes |
| memory | http://localhost:8765 | Memory service (not proxied) | No (manual) |
| install-deps | - | `bun install` | No (manual) |

## Examples

```bash
# Start all services
./scripts/tilt_up.sh

# Start only the API
./scripts/tilt_up.sh --services api

# Custom port
./scripts/tilt_up.sh --port 10395
```

## What the Script Does

1. Checks portless is installed
2. Stops any existing ImmorTerm Tilt process (by saved PID — never kills other projects)
3. Starts Tilt in background with nohup
4. Saves PID to `.tilt.pid` for safe cleanup
5. Shows dashboard URL and service endpoints

## Endpoints After Start

- Web: http://web.immorterm.localhost:1355
- API: http://api.immorterm.localhost:1355
- Memory: http://localhost:8765 (manual start, not proxied)
- Tilt UI: http://localhost:10390

## Portless

All HTTP services use [Vercel Portless](https://github.com/vercel-labs/portless) for stable, named URLs. Portless assigns dynamic ports and proxies them through `*.localhost:1355`. The portless proxy (port 1355) is shared across all projects — never stop it from teardown scripts.

## Checking Status

```bash
tilt --port 10390 get uiresources
portless list  # Show active portless routes
```

## Troubleshooting

**Port already in use**
- The script handles this automatically by stopping only the saved PID

**Infisical not logged in**
```bash
infisical login
```

**Portless not installed**
```bash
npm install -g portless
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lonormaly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
