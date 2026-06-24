---
name: ci-cd
description: Set up CI/CD pipelines for any stack and deployment target. Use when asked to "add CI", "set up GitHub Actions", "configura el pipeline", "crea el workflow de CI", "automate tests on push", "add CD", "deploy automatically", "set up GitLab CI", or "I need a pipeline". Always asks about stack and deploy target before generating anything. Use when this capability is needed.
metadata:
  author: Blake01z
---

# CI/CD pipelines

## What this skill does
Creates CI/CD pipeline configuration tailored to your stack, Git platform,
and deployment target. Covers CI (run on every PR) and CD (deploy on merge).
Never assumes — always asks first.

## Before starting — ask the user

1. **Git platform** — GitHub / GitLab / Bitbucket?
2. **CI only or CI + CD?**
3. **Stack** — Node.js / Python / other? Package manager: npm / pnpm / yarn?
4. **Test command** — what command runs your tests?
5. **CD target** (if applicable):
   - Vercel — frontend or fullstack serverless
   - Railway / Render — backend with database
   - Docker + VPS — self-hosted, full control
   - AWS (ECS / App Runner / Lambda) — enterprise or AWS-native
   - GitLab CI with any of the above targets
6. **Branch strategy** — main only, or also a staging branch?

## Core CI workflow — GitHub Actions (all stacks)

```yaml
# .github/workflows/ci.yml
name: CI

on:
  pull_request:
    branches: [main, develop]
  push:
    branches: [main]

jobs:
  ci:
    name: Test & Lint
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: pnpm          # change to npm or yarn if needed

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Lint
        run: pnpm lint

      - name: Type check
        run: pnpm typecheck    # remove this step if not using TypeScript

      - name: Test
        run: pnpm test --run   # --run for Vitest | --watchAll=false for Jest
```

## CD by deployment target

### Vercel — frontend or fullstack serverless
```yaml
# Vercel auto-deploys via its GitHub integration — no extra workflow needed.
# Add a build check to CI if you want to catch build failures before Vercel does:
- name: Build check
  run: pnpm build
```

### Railway or Render — backend
```yaml
# Both platforms auto-deploy from the main branch via their GitHub integrations.
# Add a post-deploy health check to confirm the deploy succeeded:
- name: Wait for deployment
  run: sleep 30

- name: Health check
  run: curl --fail https://your-app.railway.app/health
```

### Docker + VPS — self-hosted
See [docker-deploy](references/docker-deploy.md) for the full build-push-deploy workflow.

### AWS — ECS or App Runner
See [aws-deploy](references/aws-deploy.md) for the full AWS deployment workflow.

### GitLab CI
See [gitlab-ci](references/gitlab-ci.md) for the equivalent pipeline in GitLab format.

## Useful additions

### Cache dependencies (speeds up CI significantly)
```yaml
- uses: actions/cache@v4
  with:
    path: ~/.pnpm-store
    key: ${{ runner.os }}-pnpm-${{ hashFiles('**/pnpm-lock.yaml') }}
    restore-keys: |
      ${{ runner.os }}-pnpm-
```

### Run only on changed files — useful for monorepos
```yaml
- uses: dorny/paths-filter@v3
  id: changes
  with:
    filters: |
      frontend:
        - 'packages/frontend/**'
      backend:
        - 'packages/backend/**'
```

### Inject secrets from GitHub
```yaml
env:
  DATABASE_URL: ${{ secrets.DATABASE_URL }}
  API_KEY: ${{ secrets.API_KEY }}
# Add secrets at: GitHub repo → Settings → Secrets and variables → Actions
```

## Hard rules

- Always pin action versions (`@v4` not `@latest`) — prevents supply chain attacks
- Never hardcode secrets — always use `${{ secrets.NAME }}`
- CI must pass before merge — set as a required status check in branch protection rules
- Cache aggressively — a slow CI pipeline is a pipeline people skip
- On failure: let the workflow fail loudly — never suppress errors with `|| true`
- Keep jobs under 10 minutes — split into parallel jobs if needed

---
> Source: [Blake01z/ARCHSTACK](https://github.com/Blake01z/ARCHSTACK) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
