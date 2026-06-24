---
name: ci-cd-pipelines
description: > Use when this capability is needed.
metadata:
  author: naKarthikSurya
---

# CI/CD Pipelines Skill

## Goal

Automate the full software delivery lifecycle — from code push to production deployment —
with quality gates that prevent broken or insecure code from reaching production.

## When to Use

- A new repository needs CI/CD configured from scratch.
- An existing pipeline needs a new stage (test, build, deploy, rollback).
- A deployment to a new environment (staging, production) must be automated.
- A CI failure must be diagnosed and fixed.

## GitHub Actions — Full Pipeline

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  # --- Stage 1: Lint & Test ---
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '20', cache: 'npm' }
      - run: npm ci
      - run: npm run lint
      - run: npm run test -- --coverage
      - run: npm run test:e2e

  # --- Stage 2: Build Docker Image ---
  build:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - uses: docker/build-push-action@v5
        with:
          push: true
          tags: ghcr.io/${{ github.repository }}:${{ github.sha }}

  # --- Stage 3: Deploy to Production ---
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Deploy to server
        uses: appleboy/ssh-action@v1
        with:
          host: ${{ secrets.PROD_HOST }}
          username: ${{ secrets.PROD_USER }}
          key: ${{ secrets.PROD_SSH_KEY }}
          script: |
            docker pull ghcr.io/${{ github.repository }}:${{ github.sha }}
            docker-compose up -d --no-deps api
```

## Pipeline Stage Rules

| Stage | Must Include |
|---|---|
| Test | Lint, unit tests, integration tests, coverage check |
| Build | Docker build, image scan (Trivy), push to registry |
| Deploy Staging | Auto-deploy on `develop` branch merge |
| Deploy Production | Manual approval gate OR `main` branch only |

## Secrets Management in CI
- All secrets in GitHub/GitLab Secrets — never in workflow files.
- Use `environment` protection rules for production deployments.
- Rotate secrets every 90 days.

## Review Checklist

- [ ] Tests run on every PR
- [ ] Build only runs after tests pass
- [ ] Production deploy only triggers on `main`
- [ ] All secrets in CI secrets store, not hardcoded
- [ ] Rollback procedure documented
- [ ] Image vulnerability scan included in build stage

---
> Source: [naKarthikSurya/Talos](https://github.com/naKarthikSurya/Talos) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
