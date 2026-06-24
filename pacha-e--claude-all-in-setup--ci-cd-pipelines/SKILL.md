---
name: cicd-pipelines
description: Generate and manage CI/CD pipelines for GitHub Actions, GitLab CI, and Bitbucket Pipelines. Covers build/test/deploy workflows, secrets, caching, matrix builds, and deployment targets. Use when this capability is needed.
metadata:
  author: Pacha-e
---

# CI/CD Pipeline Generator

When the user asks to create, fix, or improve a CI/CD pipeline, follow these steps:

1. **Detect** the project type (language, framework, package manager) from the codebase
2. **Ask** which platform if unclear (default: GitHub Actions)
3. **Generate** the workflow file in the correct location
4. **Validate** YAML syntax and known gotchas before writing

## File Locations

| Platform | Path |
|---|---|
| GitHub Actions | `.github/workflows/<name>.yml` |
| GitLab CI | `.gitlab-ci.yml` |
| Bitbucket Pipelines | `bitbucket-pipelines.yml` |

## GitHub Actions (Primary)

### Workflow Structure

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup
        # setup step based on project type
      - name: Install
        # install dependencies
      - name: Build
        # build step
      - name: Test
        # test step
```

### Setup Actions by Language

- **Node.js**: `actions/setup-node@v4` with `node-version-file: '.nvmrc'` or explicit version
- **Python**: `actions/setup-python@v5` with `python-version`
- **Go**: `actions/setup-go@v5` with `go-version-file: 'go.mod'`
- **Rust**: `dtolnay/rust-toolchain@stable`
- **Java**: `actions/setup-java@v4` with `distribution: 'temurin'`
- **.NET**: `actions/setup-dotnet@v4`

### Caching Strategies

Always add caching. Use the built-in cache from setup actions when available:

```yaml
# Node - built-in cache
- uses: actions/setup-node@v4
  with:
    node-version: 20
    cache: 'npm'  # or 'pnpm', 'yarn'

# Generic cache
- uses: actions/cache@v4
  with:
    path: |
      ~/.cache/pip
      node_modules
    key: ${{ runner.os }}-${{ hashFiles('**/lockfile') }}
    restore-keys: ${{ runner.os }}-
```

### Matrix Builds

Use for multi-version testing:

```yaml
strategy:
  fail-fast: false
  matrix:
    node-version: [18, 20, 22]
    os: [ubuntu-latest, windows-latest]
```

### Secrets and Environment Variables

- Reference secrets: `${{ secrets.SECRET_NAME }}`
- Set env at workflow/job/step level with `env:`
- Use `environment:` for deployment protection rules
- Never hardcode secrets -- always use repository or environment secrets
- For OIDC (AWS, GCP), use `permissions: id-token: write`

```yaml
jobs:
  deploy:
    environment: production
    env:
      NODE_ENV: production
    steps:
      - run: echo "Deploying"
        env:
          API_KEY: ${{ secrets.API_KEY }}
```

### Common Pipeline Patterns

**Lint + Test + Build (parallel where possible):**

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm run lint

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm test

  build:
    needs: [lint, test]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm run build
```

**PR Checks with status gates:**

```yaml
on:
  pull_request:
    types: [opened, synchronize, reopened]

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

### Deployment Targets

**Vercel:**
```yaml
- uses: amondnet/vercel-action@v25
  with:
    vercel-token: ${{ secrets.VERCEL_TOKEN }}
    vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
    vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
    vercel-args: '--prod'
```

**Netlify:**
```yaml
- uses: nwtgck/actions-netlify@v3
  with:
    publish-dir: './dist'
    production-deploy: true
  env:
    NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
    NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}
```

**AWS (with OIDC):**
```yaml
permissions:
  id-token: write
  contents: read
steps:
  - uses: aws-actions/configure-aws-credentials@v4
    with:
      role-to-assume: arn:aws:iam::ACCOUNT:role/ROLE
      aws-region: us-east-1
  - run: aws s3 sync ./dist s3://bucket-name
```

**Docker Registry (GHCR):**
```yaml
- uses: docker/login-action@v3
  with:
    registry: ghcr.io
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}
- uses: docker/build-push-action@v6
  with:
    push: true
    tags: ghcr.io/${{ github.repository }}:${{ github.sha }}
    cache-from: type=gha
    cache-to: type=gha,mode=max
```

## GitLab CI (Secondary)

```yaml
stages:
  - test
  - build
  - deploy

variables:
  NODE_ENV: production

test:
  stage: test
  image: node:20
  cache:
    paths: [node_modules/]
    key: $CI_COMMIT_REF_SLUG
  script:
    - npm ci
    - npm test

build:
  stage: build
  script:
    - npm run build
  artifacts:
    paths: [dist/]

deploy:
  stage: deploy
  environment: production
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
  script:
    - ./deploy.sh
```

## Bitbucket Pipelines (Secondary)

```yaml
image: node:20

definitions:
  caches:
    npm: $HOME/.npm

pipelines:
  default:
    - step:
        name: Test
        caches: [npm]
        script:
          - npm ci
          - npm test

  branches:
    main:
      - step:
          name: Deploy
          deployment: production
          script:
            - npm ci
            - npm run build
            - ./deploy.sh
```

## Best Practices Checklist

When generating pipelines, always ensure:

- [ ] Pin action versions to full SHA or major version (`@v4`, not `@main`)
- [ ] Set minimal `permissions` (least privilege)
- [ ] Add `concurrency` to cancel stale PR runs
- [ ] Cache dependencies for faster builds
- [ ] Use `fail-fast: false` in matrix if jobs are independent
- [ ] Add `timeout-minutes` to prevent stuck jobs (default: 360 min is too long)
- [ ] Use `environment` for deploy jobs requiring approval
- [ ] Separate CI (test/lint) from CD (deploy) workflows when complexity grows
- [ ] Add path filters to skip irrelevant runs: `paths-ignore: ['**.md', 'docs/**']`

---
> Source: [Pacha-e/claude-ALL-IN-SETUP](https://github.com/Pacha-e/claude-ALL-IN-SETUP) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
