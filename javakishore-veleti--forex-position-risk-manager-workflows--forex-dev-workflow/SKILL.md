---
name: forex-dev-workflow
description: Documents the local development environment setup, Docker scripts, root package.json commands, and how to start/stop/check the full FOREX stack. Use when working on DevOps scripts, docker-compose configuration, adding new services to the stack, or when the user asks how to run, stop, or check status of the application locally. Use when this capability is needed.
metadata:
  author: javakishore-veleti
---

# FOREX Dev Workflow

## Dev Environment Components

The local development stack consists of:

1. **PostgreSQL** — Shared database server (each service uses its own schema/database)
2. **Temporal Server** — Workflow engine + Temporal UI (visible at `http://localhost:8233`)
3. **Customer Profile Service** — Go service on port `8081`
4. **Trading Book Service** — Go service on port `8082`
5. **Trade Capture Worker** — Temporal worker process (no HTTP port, connects to Temporal server)
6. **Portal** — Angular dev server on port `4200`

## DevOps/Local Scripts

All scripts are in `DevOps/Local/`:

| Script | Purpose |
|--------|---------|
| `docker-all-run.sh` | Build Docker images and start all containers via Docker Compose. Accepts `--no-build` to skip image builds. |
| `docker-all-shutdown.sh` | Gracefully stop and remove all containers and networks. Accepts `--volumes` to also delete persistent data. |
| `stop.sh` | Quick-stop containers without removing them (can resume with `docker compose start`). |
| `status.sh` | Display running container status, ports, and health. Prints URLs for Temporal UI and Portal. |

## Root package.json Scripts

The root `package.json` provides a single entry point for all operations:

| npm Script | Delegates To | Purpose |
|-----------|-------------|---------|
| `start:all` | `DevOps/Local/docker-all-run.sh` | Build and start the full stack |
| `stop:all` | `DevOps/Local/docker-all-shutdown.sh` | Stop and remove all containers |
| `stop` | `DevOps/Local/stop.sh` | Quick-stop without removal |
| `status` | `DevOps/Local/status.sh` | Show container status |

## Adding a New Microservice to the Stack

When adding a new microservice:

1. Create the service directory under `Backend/Services/<service-name>/`.
2. Add a `Dockerfile` to the service directory.
3. Add the service to `docker-compose.yml` with its port, environment variables, and database dependency.
4. Add corresponding start/health-check entries in the DevOps scripts if needed.
5. Add npm script aliases in root `package.json` if the service needs individual start/stop control.
6. Update `DevOps/Local/status.sh` to include the new service's URL.

## Detailed Reference

For full environment setup instructions and Docker Compose configuration details, read [references/dev-environment.md](references/dev-environment.md).

---
> Source: [javakishore-veleti/FOREX-Position-Risk-Manager-Workflows](https://github.com/javakishore-veleti/FOREX-Position-Risk-Manager-Workflows) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
