---
name: ci-cd-pipeline
description: >- Use when this capability is needed.
metadata:
  author: Doanhaiduy
---

# Skill: CI/CD Pipeline

## Khi nào sử dụng
Kỹ năng này được kích hoạt khi cần thiết lập hoặc cải tiến CI/CD pipeline cho dự án.

## Pipeline Architecture

```
┌─────────┐    ┌──────┐    ┌──────────┐    ┌───────┐    ┌────────┐
│  Lint   │───▶│ Test │───▶│ Security │───▶│ Build │───▶│ Deploy │
│ & Type  │    │      │    │  Audit   │    │       │    │        │
└─────────┘    └──────┘    └──────────┘    └───────┘    └────────┘
   PR Only      PR Only      PR Only       main only   main+manual
```

## GitHub Actions — Full CI Pipeline

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

concurrency:
  group: ci-${{ github.ref }}
  cancel-in-progress: true

env:
  NODE_VERSION: '20'
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  # ===== Stage 1: Lint & Type Check =====
  lint:
    name: Lint & Type Check
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      - run: npm ci
      - run: npm run lint
      - run: npm run type-check
      - run: npm run format:check

  # ===== Stage 2: Unit Tests =====
  test-unit:
    name: Unit Tests
    runs-on: ubuntu-latest
    needs: lint
    timeout-minutes: 15
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      - run: npm ci
      - run: npm run test:unit -- --coverage
      - uses: codecov/codecov-action@v4
        with:
          files: ./coverage/lcov.info
          fail_ci_if_error: false

  # ===== Stage 3: Integration Tests =====
  test-integration:
    name: Integration Tests
    runs-on: ubuntu-latest
    needs: lint
    timeout-minutes: 20
    services:
      postgres:
        image: postgres:16-alpine
        env:
          POSTGRES_DB: testdb
          POSTGRES_USER: testuser
          POSTGRES_PASSWORD: testpass
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
      redis:
        image: redis:7-alpine
        ports:
          - 6379:6379
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      - run: npm ci
      - run: npm run db:migrate:test
        env:
          DATABASE_URL: postgresql://testuser:testpass@localhost:5432/testdb
      - run: npm run test:integration
        env:
          DATABASE_URL: postgresql://testuser:testpass@localhost:5432/testdb
          REDIS_URL: redis://localhost:6379

  # ===== Stage 4: Security Audit =====
  security:
    name: Security Audit
    runs-on: ubuntu-latest
    needs: lint
    timeout-minutes: 10
    steps:
      - uses: actions/checkout@v4
      - run: npm audit --audit-level=high
      - uses: github/codeql-action/init@v3
        with:
          languages: javascript-typescript
      - uses: github/codeql-action/analyze@v3

  # ===== Stage 5: Build Docker Image =====
  build:
    name: Build & Push Image
    runs-on: ubuntu-latest
    needs: [test-unit, test-integration, security]
    if: github.ref == 'refs/heads/main'
    permissions:
      contents: read
      packages: write
    steps:
      - uses: actions/checkout@v4
      - uses: docker/setup-buildx-action@v3
      - uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - uses: docker/metadata-action@v5
        id: meta
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          tags: |
            type=sha,prefix=
            type=ref,event=branch
            type=semver,pattern={{version}}
      - uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

## GitHub Actions — CD Pipeline (Deploy)

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  workflow_run:
    workflows: ["CI"]
    branches: [main]
    types: [completed]
  workflow_dispatch:
    inputs:
      environment:
        description: 'Target environment'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production

jobs:
  deploy-staging:
    name: Deploy to Staging
    runs-on: ubuntu-latest
    if: ${{ github.event.workflow_run.conclusion == 'success' || github.event.inputs.environment == 'staging' }}
    environment: staging
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to staging
        run: echo "Deploy to staging environment"
        # Add actual deployment commands here

  deploy-production:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: deploy-staging
    if: github.event.inputs.environment == 'production'
    environment: production  # Requires manual approval
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to production
        run: echo "Deploy to production environment"
        # Add actual deployment commands here
```

## Branch Strategy

```
main ────────────────────────────────────── Production
  │
  └── develop ──────────────────────────── Staging
        │
        ├── feature/TICKET-123-user-auth ── Feature branches
        ├── bugfix/TICKET-456-login-fix
        └── hotfix/TICKET-789-critical-fix ── Hotfix (→ main directly)
```

## Package Scripts (package.json)

```json
{
  "scripts": {
    "dev": "nest start --watch",
    "build": "nest build",
    "start:prod": "node dist/main.js",
    "lint": "eslint 'src/**/*.ts'",
    "lint:fix": "eslint 'src/**/*.ts' --fix",
    "format:check": "prettier --check 'src/**/*.ts'",
    "format": "prettier --write 'src/**/*.ts'",
    "type-check": "tsc --noEmit",
    "test:unit": "jest --testPathPattern='.spec.ts$'",
    "test:integration": "jest --testPathPattern='.integration.ts$' --runInBand",
    "test:e2e": "jest --testPathPattern='.e2e.ts$' --runInBand",
    "test:ci": "jest --ci --coverage --runInBand",
    "db:migrate": "node scripts/migrate.js up",
    "db:migrate:test": "DATABASE_URL=$TEST_DATABASE_URL node scripts/migrate.js up",
    "db:rollback": "node scripts/migrate.js down",
    "db:seed": "node scripts/seed.js"
  }
}
```

## Quality Checklist
- [ ] CI runs on both push and pull_request?
- [ ] Lint runs before tests (fast fail)?
- [ ] Unit and integration tests separated?
- [ ] Database service containers configured with health checks?
- [ ] Security audit included?
- [ ] Docker build only on main branch?
- [ ] Concurrency configured (cancel redundant runs)?
- [ ] Timeouts set on all jobs?
- [ ] Deployment requires approval for production?
- [ ] Secrets stored in GitHub Secrets (not in code)?

---
> Source: [Doanhaiduy/AI_agentic](https://github.com/Doanhaiduy/AI_agentic) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
