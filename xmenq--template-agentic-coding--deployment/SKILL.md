---
name: deployment
description: Guidelines for CI/CD, containerization, infrastructure, and release management Use when this capability is needed.
metadata:
  author: xmenq
---

# Deployment Skill

## When to use this skill

Use when setting up CI/CD pipelines, containerizing applications, configuring infrastructure, or managing releases.

---

## Deployment Principles

### 1. Automate everything
- If you deploy manually more than once, automate it
- Manual steps = human error risk

### 2. Deploy small and often
- Small deployments are easier to debug, roll back, and understand
- Ship multiple times per day, not once per sprint

### 3. Rollback is always the first response
- If something breaks, revert first, investigate second
- Every deployment must be reversible within minutes

---

## CI/CD Pipeline

### Standard pipeline stages
```
Code Push → Lint → Typecheck → Unit Test → Build → Integration Test → Deploy → Smoke Test
```

| Stage | Purpose | Fail behavior |
|-------|---------|---------------|
| **Lint** | Code style & rules | Block merge |
| **Typecheck** | Type safety | Block merge |
| **Unit test** | Logic correctness | Block merge |
| **Build** | Artifact creation | Block deploy |
| **Integration test** | Component interactions | Block deploy |
| **Deploy** | Release to environment | Auto-rollback on failure |
| **Smoke test** | Critical paths work post-deploy | Alert + auto-rollback |

### Branch strategy
| Branch | Purpose | Deploys to |
|--------|---------|-----------|
| `main` | Production-ready code | Production |
| `staging` | Pre-production testing | Staging environment |
| Feature branches | Work in progress | Preview environment (optional) |

---

## Containerization

### Dockerfile best practices
```dockerfile
# 1. Use specific version tags (not :latest)
FROM node:20-alpine AS base

# 2. Copy dependency files first (cache layer)
COPY package.json package-lock.json ./
RUN npm ci --production

# 3. Copy source code (changes more often)
COPY src/ ./src/

# 4. Use multi-stage builds (smaller final image)
FROM base AS production
# ... production-only setup

# 5. Run as non-root user
USER nobody

# 6. Use health check
HEALTHCHECK CMD curl -f http://localhost:3000/health || exit 1
```

### Container rules
- **Pin base image versions** — `node:20.11-alpine`, not `node:latest`
- **Multi-stage builds** — separate build dependencies from runtime
- **Non-root user** — never run as root in production
- **`.dockerignore`** — exclude `node_modules`, `.git`, tests, docs
- **Health checks** — containers should report their own health
- **One process per container** — don't bundle multiple services

---

## Environment Management

### Environment hierarchy
```
local → dev → staging → production
```

### Rules
- **Environment parity** — staging should mirror production as closely as possible
- **Environment-specific config via env vars** — never hardcode URLs, credentials, or flags
- **Configuration hierarchy:**
  1. Environment variables (highest priority)
  2. Environment-specific config file
  3. Default config file (lowest priority)

### Required env vars (document these)
```
# App
APP_PORT=3000
APP_ENV=production
LOG_LEVEL=info

# Database
DATABASE_URL=postgres://...
DATABASE_POOL_SIZE=10

# Auth
JWT_SECRET=...
SESSION_TTL_SECONDS=3600

# External services
API_KEY_SERVICE_X=...
```

---

## Release Management

### Versioning
Use **semantic versioning** (SemVer): `MAJOR.MINOR.PATCH`
- **MAJOR** — breaking changes
- **MINOR** — new features, backward compatible
- **PATCH** — bug fixes, backward compatible

### Release checklist
- [ ] All CI checks pass on release branch
- [ ] Changelog updated with user-facing changes
- [ ] Database migrations tested on staging
- [ ] Feature flags set correctly for gradual rollout
- [ ] Rollback plan documented and tested
- [ ] Monitoring dashboards ready
- [ ] On-call team notified

### Rollback procedure
1. **Detect** — monitoring alerts or user reports
2. **Decide** — rollback if fix isn't obvious within 15 minutes
3. **Revert** — deploy previous known-good version
4. **Communicate** — notify stakeholders
5. **Investigate** — root-cause analysis after rollback
6. **Fix forward** — fix the issue, deploy again

---

## Monitoring Post-Deploy

After every deployment, watch for 30 minutes:
- Error rate (should stay flat or decrease)
- Response latency (should stay flat or decrease)
- Resource usage (CPU, memory)
- Key business metrics (conversions, signups)

---

## Infrastructure as Code

- **Define infrastructure in code** — Terraform, Pulumi, CloudFormation
- **Version control infra configs** — same repo or dedicated infra repo
- **Review infra changes like code** — PR process for infrastructure
- **Test in staging first** — never apply untested infra changes to production

---

## PR Checklist for Deployment Changes

- [ ] Pipeline tested with a dry run
- [ ] Dockerfile follows best practices (pinned versions, non-root, multi-stage)
- [ ] Environment variables documented
- [ ] Rollback procedure documented and tested
- [ ] Health checks configured
- [ ] Monitoring/alerting updated
- [ ] Staging deployment verified before production

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xmenq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
