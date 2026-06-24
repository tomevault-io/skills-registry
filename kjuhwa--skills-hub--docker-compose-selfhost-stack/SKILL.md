---
name: docker-compose-selfhost-stack
description: Single docker-compose.selfhost.yml that starts postgres + backend + frontend for one-command self-hosting, with sensible defaults and a one-shot make target. Use when this capability is needed.
metadata:
  author: kjuhwa
---

## When to use

- You're shipping an open-source product with a "self-host in 5 minutes" story.
- Users should get a working local stack with one command, and `.env` should be safe-by-default.

## Steps

1. Create `docker-compose.selfhost.yml` with three services and a healthcheck-gated backend:
   ```yaml
   name: myapp
   services:
     postgres:
       image: pgvector/pgvector:pg17
       environment:
         POSTGRES_DB: ${POSTGRES_DB:-myapp}
         POSTGRES_USER: ${POSTGRES_USER:-myapp}
         POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-myapp}
       volumes: [pgdata:/var/lib/postgresql/data]
       healthcheck:
         test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER:-myapp}"]
         interval: 5s
         retries: 5
     backend:
       build: { context: ., dockerfile: Dockerfile }
       depends_on: { postgres: { condition: service_healthy } }
       ports: ["${PORT:-8080}:8080"]
       environment:
         DATABASE_URL: postgres://${POSTGRES_USER:-myapp}:${POSTGRES_PASSWORD:-myapp}@postgres:5432/${POSTGRES_DB:-myapp}
         JWT_SECRET: ${JWT_SECRET:-change-me-in-production}
         APP_ENV: ${APP_ENV:-production}   # safe default
     frontend:
       build:
         context: .
         dockerfile: Dockerfile.web
         args:
           REMOTE_API_URL: http://backend:8080
       depends_on: [backend]
       ports: ["${FRONTEND_PORT:-3000}:3000"]
   volumes:
     pgdata:
   ```
2. Add a `make selfhost` target that auto-creates `.env` and generates a random JWT secret on first run:
   ```makefile
   selfhost:
       @if [ ! -f .env ]; then \
         cp .env.example .env; \
         JWT=$$(openssl rand -hex 32); \
         sed -i "s/^JWT_SECRET=.*/JWT_SECRET=$$JWT/" .env; \
       fi
       docker compose -f docker-compose.selfhost.yml up -d --build
       @for i in $$(seq 1 30); do \
         curl -sf http://localhost:$${PORT:-8080}/health > /dev/null && break; \
         sleep 2; \
       done
       @curl -sf http://localhost:$${PORT:-8080}/health && echo "✓ Stack is running!"
   ```
3. `.env.example` lists every knob (ports, email provider, OAuth) with safe defaults and comments for secrets.

## Example

User journey:

```bash
git clone https://github.com/org/app.git
cd app
make selfhost
# ✓ Stack is running!
# Frontend: http://localhost:3000
# Backend:  http://localhost:8080
```

## Caveats

- `JWT_SECRET` defaulting to `change-me-in-production` is intentional: users who skip the `.env` step get a loud, searchable string in logs. Auto-generate via `openssl rand -hex 32` to silently make the default safe.
- Default `APP_ENV=production` so any "dev master password" feature is off unless explicitly enabled.
- The 30-attempt health poll covers slow first-run Docker image pulls.

---
> Source: [kjuhwa/skills-hub](https://github.com/kjuhwa/skills-hub) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
