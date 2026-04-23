---
name: devops-infrastructure
description: Manage deployment, Docker, CI/CD, server hardening, and infrastructure security. EXCLUSIVE to devops-engineer agent. Use when this capability is needed.
metadata:
  author: mgkyawzayya
---
# DevOps Infrastructure

**Exclusive to:** `devops-engineer` agent

## Instructions

1. Review existing infrastructure files (Dockerfile, docker-compose, .github/workflows)
2. Understand deployment requirements
3. Propose configuration with rollback plan
4. Implement with safety checks
5. Verify deployment succeeds

## Docker Patterns

### Multi-stage Build
```dockerfile
FROM composer:2 AS vendor
# Install dependencies

FROM node:20-alpine AS assets
# Build frontend

FROM php:8.3-fpm-alpine
# Final production image
```

### Docker Compose
```yaml
services:
  app:
    build: .
  db:
    image: mysql:8.0
  redis:
    image: redis:alpine
```

## CI/CD Workflow

```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: composer test
```

## Deployment Checklist
- [ ] Tests pass
- [ ] Environment variables set
- [ ] Database migrations ready
- [ ] Backup exists
- [ ] SSL configured

## Examples
- "Create Dockerfile for Laravel"
- "Set up GitHub Actions pipeline"
- "Configure production environment"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mgkyawzayya) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
