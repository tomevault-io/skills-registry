---
name: cicd
description: CI/CD pipeline best practices including GitHub Actions, testing, and deployment strategies. Use when this capability is needed.
metadata:
  author: kprsnt2
---

# CI/CD Best Practices

## Pipeline Design
- Keep pipelines fast (< 10 min)
- Fail fast (lint/test first)
- Cache dependencies
- Use parallel jobs
- Make builds reproducible

## GitHub Actions
- Use specific action versions
- Use composite actions for reuse
- Store secrets in GitHub Secrets
- Use matrix for multi-version testing
- Use artifacts for build outputs

## Testing in CI
- Run unit tests on every push
- Run integration tests on PR
- Run E2E tests before deploy
- Generate coverage reports
- Fail on coverage drops

## Deployment
- Use blue/green or canary deployments
- Automate staging deployments
- Require approval for production
- Implement rollback procedures
- Use feature flags

## Security
- Scan dependencies (Dependabot, Snyk)
- Scan Docker images
- Run SAST/DAST
- Rotate secrets regularly
- Use OIDC for cloud auth

## Artifacts
- Version artifacts semantically
- Sign artifacts
- Store in artifact registry
- Clean up old artifacts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kprsnt2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
