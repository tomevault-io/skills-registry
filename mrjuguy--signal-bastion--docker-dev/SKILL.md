---
name: docker-dev
description: Managing Docker containers for development. Use when this capability is needed.
metadata:
  author: mrjuguy
---

# Docker Development Skill

## Commands

- **Start**: `docker-compose up -d --build` (Detached mode, rebuild images).
- **Logs**: `docker-compose logs -f [service_name]` (Follow logs).
- **Stop**: `docker-compose down`.
- **Clean**: `docker-compose down -v` (Removes volumes - WARNING: Deletes DB data!).

## Patterns

- **Secrets**: Files like `kalshi.key` are mounted as volumes. NEVER modify the Dockerfile to `COPY` secrets in.
- **Network**: Services communicate via service names (e.g., `db`, `redis`), not `localhost`.
- **Database**: The database is available at `db:5432`.

## Debugging

- If a container crashes immediately, checks logs: `docker-compose logs [service]`.
- To inspect a running container: `docker exec -it [container_id] /bin/bash`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mrjuguy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
