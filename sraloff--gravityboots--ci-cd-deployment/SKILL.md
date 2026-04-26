---
name: ci-cd-deployment
description: GitHub Actions, Vercel/Netlify, and automated testing patterns. Use when this capability is needed.
metadata:
  author: sraloff
---

# CI/CD & Deployment

## When to use this skill
- Configuring GitHub Actions workflows.
- Setting up automated deployments.
- Configuring test runners in CI.

## 1. GitHub Actions
- **Triggers**: `on: push` for branches, `on: pull_request` for verification.
- **Caching**: Always cache dependencies (npm, pip, composer) to speed up builds.
  ```yaml
  - uses: actions/setup-node@v4
    with:
      node-version: 18
      cache: 'npm'
  ```

## 2. Deployment
- **Atomic**: Deployment should be atomic (zero downtime).
- **Migration**: Run DB migrations during deployment, but ensure backward compatibility.
- **Secrets**: Use repo secrets; never strict commit credentials.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sraloff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
