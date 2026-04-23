---
name: docker-patterns
description: Use when creating or optimizing Dockerfiles and docker-compose configurations. Covers multi-stage builds, layer caching, security hardening, health checks, and volume management.
metadata:
  author: canivel
---

# Docker Best Practices

## Multi-Stage Build (Node.js)

```dockerfile
# Stage 1: Install dependencies
FROM node:20-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --ignore-scripts

# Stage 2: Build
FROM node:20-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Stage 3: Production
FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production

# Security: run as non-root user
RUN addgroup --system --gid 1001 appgroup && \
    adduser --system --uid 1001 appuser
USER appuser

COPY --from=builder --chown=appuser:appgroup /app/dist ./dist
COPY --from=builder --chown=appuser:appgroup /app/node_modules ./node_modules
COPY --from=builder --chown=appuser:appgroup /app/package.json ./

EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s \
  CMD wget --no-verbose --tries=1 --spider http://localhost:3000/health || exit 1

CMD ["node", "dist/server.js"]
```

## Layer Caching Strategy

```dockerfile
# Order from least to most frequently changed

# 1. Base image and system deps (rarely change)
FROM node:20-alpine
RUN apk add --no-cache curl

# 2. Package files (change when deps change)
COPY package.json package-lock.json ./
RUN npm ci

# 3. Source code (changes most often)
COPY . .
RUN npm run build
```

## .dockerignore

```
node_modules
dist
.git
.env
.env.*
*.md
.vscode
.idea
coverage
.next
docker-compose*.yml
Dockerfile*
```

## Security Hardening

```dockerfile
# Always pin exact base image versions in production
FROM node:20.11.0-alpine3.19

# Never run as root
RUN addgroup --system app && adduser --system --ingroup app app
USER app

# Use COPY, not ADD (ADD auto-extracts archives, potential risk)
COPY . .

# Do not store secrets in images - use runtime environment variables
# BAD: ENV API_KEY=secret123
# GOOD: pass at runtime with docker run -e API_KEY=secret123

# Scan for vulnerabilities
# docker scout quickview myimage:latest
```

## Docker Compose - Development

```yaml
# docker-compose.yml
services:
  app:
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - .:/app
      - /app/node_modules    # anonymous volume to preserve container's node_modules
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgresql://user:pass@db:5432/otto_dev
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:16-alpine
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: otto_dev
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d otto_dev"]
      interval: 5s
      timeout: 3s
      retries: 5

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 3s

volumes:
  pgdata:
```

## Docker Compose - Production

```yaml
# docker-compose.prod.yml
services:
  app:
    image: ghcr.io/org/otto:${TAG:-latest}
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=${DATABASE_URL}
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:3000/health"]
      interval: 30s
      timeout: 5s
      retries: 3
      start_period: 15s
    deploy:
      resources:
        limits:
          memory: 512M
          cpus: "0.5"
```

## Health Checks

```ts
// server health endpoint
app.get('/health', async (req, res) => {
  try {
    await db.execute(sql`SELECT 1`);  // check DB connection
    res.status(200).json({ status: 'healthy', timestamp: new Date().toISOString() });
  } catch (err) {
    res.status(503).json({ status: 'unhealthy', error: 'database unreachable' });
  }
});
```

## Volume Management

```yaml
# Named volumes for persistent data
volumes:
  pgdata:
    driver: local

# Bind mounts for development source code
volumes:
  - ./src:/app/src

# tmpfs for ephemeral data (no disk writes)
tmpfs:
  - /tmp
```

## Anti-Patterns

- NEVER use `latest` tag in production. Pin exact versions.
- NEVER run containers as root. Always create and use a non-root user.
- NEVER store secrets in Dockerfiles or docker-compose files. Use runtime env vars or secrets managers.
- NEVER install dev dependencies in production images. Use `npm ci --omit=dev`.
- NEVER use `ADD` when `COPY` suffices. `ADD` has implicit behaviors like archive extraction.
- NEVER skip `.dockerignore`. Without it, `COPY . .` sends everything including `node_modules` and `.git`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canivel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
