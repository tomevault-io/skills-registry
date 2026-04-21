---
name: docker-deployment
description: Help with Docker containerization and deployment tasks. Use when working with Dockerfiles, docker-compose files, container builds, or when the user asks about Docker deployment. Use when this capability is needed.
metadata:
  author: luminous-banking
---

# Docker Deployment

Assist with Docker containerization, image building, and deployment workflows.

## Quick Start

When helping with Docker tasks:

1. Identify the task type (build, run, compose, debug)
2. Review existing Docker configuration
3. Implement changes following best practices
4. Test the configuration

## Dockerfile Best Practices

### Multi-Stage Builds
Use multi-stage builds to minimize image size:

```dockerfile
# Build stage
FROM python:3.11-slim as builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --user -r requirements.txt

# Production stage
FROM python:3.11-slim
WORKDIR /app
COPY --from=builder /root/.local /root/.local
COPY . .
ENV PATH=/root/.local/bin:$PATH
CMD ["python", "main.py"]
```

### Layer Optimization
- Order instructions from least to most frequently changing
- Combine RUN commands to reduce layers
- Use `.dockerignore` to exclude unnecessary files

### Security
- Use specific image tags (not `latest`)
- Run as non-root user when possible
- Scan images for vulnerabilities
- Don't include secrets in images

## Docker Compose Patterns

### Development Setup
```yaml
services:
  app:
    build: .
    volumes:
      - .:/app  # Mount for hot reload
    environment:
      - DEBUG=true
    ports:
      - "8000:8000"
```

### Production Setup
```yaml
services:
  app:
    image: myapp:${VERSION:-latest}
    restart: unless-stopped
    environment:
      - DEBUG=false
    deploy:
      resources:
        limits:
          memory: 512M
```

## Common Tasks

### Build and Run
```bash
# Build image
docker build -t myapp:latest .

# Run container
docker run -d -p 8000:8000 myapp:latest

# View logs
docker logs -f container_name
```

### Compose Commands
```bash
# Start services
docker-compose up -d

# Rebuild and start
docker-compose up -d --build

# View logs
docker-compose logs -f

# Stop and remove
docker-compose down
```

### Debugging
```bash
# Shell into running container
docker exec -it container_name /bin/bash

# Inspect container
docker inspect container_name

# Check resource usage
docker stats
```

## Troubleshooting Checklist

- [ ] Port conflicts resolved
- [ ] Volume mounts have correct permissions
- [ ] Environment variables are set
- [ ] Network connectivity between services
- [ ] Sufficient disk space for images
- [ ] Memory limits appropriate for workload

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luminous-banking) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
