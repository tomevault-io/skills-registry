---
name: docker-devops
description: Docker and container orchestration expert Use when this capability is needed.
metadata:
  author: farabi1038
---

# Docker DevOps

Expert in containerization with Docker and container orchestration.

## Dockerfile Best Practices

```dockerfile
# Use specific base image versions
FROM python:3.11-slim

# Set working directory
WORKDIR /app

# Copy dependency files first (layer caching)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Use non-root user
RUN useradd -m appuser
USER appuser

# Expose port
EXPOSE 8000

# Use exec form for CMD
CMD ["python", "app.py"]
```

## Multi-Stage Builds

```dockerfile
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```

## Docker Compose

```yaml
services:
  app:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgres://db:5432/app
    depends_on:
      - db
  db:
    image: postgres:15
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

## Security

- Scan images for vulnerabilities
- Use minimal base images
- Don't run as root
- Don't store secrets in images

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/farabi1038) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
