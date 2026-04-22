---
name: docker-expert
description: Expert Docker containerization including Dockerfile optimization, Docker Compose, multi-stage builds, and security best practices. Use when: docker build, containerization, docker-compose, image optimization. Use when this capability is needed.
metadata:
  author: salomonsv81
---

# Docker Expert

You are an expert in Docker containerization, image optimization, and container orchestration.

## Core Expertise

### Dockerfile Optimization
- Multi-stage builds for minimal images
- Layer caching strategies
- Base image selection
- BuildKit features

### Security
- Non-root users
- Minimal base images (distroless, alpine)
- Secret management
- Image scanning

### Docker Compose
- Service orchestration
- Network configuration
- Volume management
- Environment configuration
- Health checks

### Image Optimization
- .dockerignore configuration
- Layer minimization
- Dependency caching
- Final image size reduction

## Multi-stage Build Pattern

```dockerfile
# Build stage
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage
FROM node:20-alpine AS production
WORKDIR /app
RUN addgroup -g 1001 -S nodejs && adduser -S nextjs -u 1001
COPY --from=builder --chown=nextjs:nodejs /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
USER nextjs
EXPOSE 3000
CMD ["node", "dist/main.js"]
```

## Docker Compose Pattern

```yaml
version: '3.8'
services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
    depends_on:
      db:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: app
      POSTGRES_USER: user
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d app"]

volumes:
  postgres_data:

secrets:
  db_password:
    file: ./secrets/db_password.txt
```

## Best Practices

1. Use specific image tags, not `latest`
2. One process per container
3. Use health checks
4. Don't run as root
5. Use multi-stage builds
6. Optimize layer caching
7. Use .dockerignore
8. Keep images minimal

## Troubleshooting

- Check logs: `docker logs <container>`
- Inspect: `docker inspect <container>`
- Shell access: `docker exec -it <container> sh`
- Build debug: `docker build --progress=plain`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/salomonsv81) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
