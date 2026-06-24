---
name: ops-docker-compose-orchestration
description: Managing multi-container applications and local development environments. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Docker Compose Orchestration

Docker Compose allows you to define and run multi-container applications using a single YAML file.

## Configuration (docker-compose.yml)
- **Services**: Define your app, database, cache, and reverse proxy.
- **Environment**: Inject secrets and config via `.env` files.

## Local Development
- **Bind Mounts**: Sync code between host and container for "live-reload" development.
- **Profiles**: Start subsets of services (e.g., only the DB) with `--profile`.

## Best Practices
- **Healthchecks**: Ensure dependent services (like the DB) are ready before the app starts.
- **Named Volumes**: Use volumes for persistent data (PostgreSQL, Redis).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
