---
name: docker-containerization
description: Dockerfiles, multi-stage builds, and compose for dev/prod. Use when this capability is needed.
metadata:
  author: sraloff
---

# Docker & Containerization

## When to use this skill
- Creating `Dockerfile` or `docker-compose.yml`.
- Optimizing image size.
- Debugging container networking.

## 1. Dockerfile Best Practices
- **Multi-Stage**: Use multi-stage builds to keep production images small (e.g., build in `node:20`, run in `node:20-alpine`).
- **Ordering**: Place frequent changes (code copying) AFTER infrequent changes (npm install) to leverage layer caching.
- **User**: Don't run as root. User `USER node` or create a non-root user.

## 2. Docker Compose
- **Services**: Define services clearly (`app`, `db`, `redis`).
- **Volumes**: Use named volumes for persistence (`postgres_data:/var/lib/postgresql/data`).
- **Env**: Use `.env` file for environment variables.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sraloff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
