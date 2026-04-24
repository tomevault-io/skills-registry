---
name: tools-github-actions
description: GitHub Actions workflow authoring for CI/CD pipelines, job configuration, matrix builds, secrets, and common automation patterns. Use when this capability is needed.
metadata:
  author: tjboudreaux
---

# GitHub Actions

## Overview
GitHub Actions automates CI/CD workflows directly in GitHub. Use this skill for creating workflows, configuring jobs, and implementing common automation patterns.

## When to Use
- Setting up CI/CD pipelines
- Automating tests, builds, deployments
- Creating reusable workflows
- Matrix testing across versions
- Scheduled tasks and automation

## Workflow Structure

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: npm test
```

## Triggers (on:)

### Push & PR
```yaml
on:
  push:
    branches: [main, develop]
    paths:
      - 'src/**'
      - '!src/**/*.md'
    tags:
      - 'v*'
  pull_request:
    branches: [main]
    types: [opened, synchronize, reopened]
```

### Schedule (Cron)
```yaml
on:
  schedule:
    - cron: '0 0 * * *'    # Daily at midnight
    - cron: '0 */6 * * *'  # Every 6 hours
```

### Manual & Dispatch
```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Deploy environment'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production
  repository_dispatch:
    types: [deploy]
```

### Release
```yaml
on:
  release:
    types: [published, created]
```

## Job Configuration

### Basic Job
```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    timeout-minutes: 30
    steps:
      - uses: actions/checkout@v4
      - run: npm test
```

### Job Dependencies
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: npm run build
      
  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - run: ./deploy.sh
```

### Conditional Jobs
```yaml
jobs:
  deploy:
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    
  notify:
    if: failure()
    needs: [build, test]
```

## Matrix Builds

```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]
        node: [18, 20, 22]
        exclude:
          - os: windows-latest
            node: 18
        include:
          - os: ubuntu-latest
            node: 22
            experimental: true
      fail-fast: false
      max-parallel: 4
    steps:
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
```

## Common Actions

### Checkout
```yaml
- uses: actions/checkout@v4
  with:
    fetch-depth: 0              # Full history
    submodules: true            # Include submodules
    token: ${{ secrets.PAT }}   # For private repos
```

### Setup Node
```yaml
- uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'npm'                # or 'pnpm', 'yarn'
    registry-url: 'https://npm.pkg.github.com'
```

### Cache
```yaml
- uses: actions/cache@v4
  with:
    path: |
      ~/.npm
      node_modules
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-
```

### Upload/Download Artifacts
```yaml
- uses: actions/upload-artifact@v4
  with:
    name: build-output
    path: dist/
    retention-days: 5

- uses: actions/download-artifact@v4
  with:
    name: build-output
    path: dist/
```

## Environment Variables

```yaml
env:
  NODE_ENV: production          # Workflow level

jobs:
  build:
    env:
      CI: true                  # Job level
    steps:
      - run: echo $MY_VAR
        env:
          MY_VAR: step-level    # Step level
```

### Using Secrets
```yaml
steps:
  - run: ./deploy.sh
    env:
      API_KEY: ${{ secrets.API_KEY }}
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
```

## Outputs & Sharing Data

### Job Outputs
```yaml
jobs:
  build:
    outputs:
      version: ${{ steps.version.outputs.value }}
    steps:
      - id: version
        run: echo "value=$(cat package.json | jq -r .version)" >> $GITHUB_OUTPUT
        
  deploy:
    needs: build
    steps:
      - run: echo "Deploying version ${{ needs.build.outputs.version }}"
```

### Step Outputs
```yaml
steps:
  - id: vars
    run: |
      echo "sha_short=$(git rev-parse --short HEAD)" >> $GITHUB_OUTPUT
      echo "branch=${GITHUB_REF#refs/heads/}" >> $GITHUB_OUTPUT
  - run: echo "SHA: ${{ steps.vars.outputs.sha_short }}"
```

## Contexts & Expressions

### Common Contexts
```yaml
${{ github.sha }}               # Commit SHA
${{ github.ref }}               # refs/heads/main
${{ github.ref_name }}          # main
${{ github.event_name }}        # push, pull_request
${{ github.actor }}             # User who triggered
${{ github.repository }}        # owner/repo
${{ runner.os }}                # Linux, Windows, macOS
${{ secrets.TOKEN }}            # Secret value
${{ vars.MY_VAR }}              # Repository variable
```

### Expressions
```yaml
if: ${{ github.event_name == 'push' }}
if: ${{ contains(github.event.head_commit.message, '[skip ci]') }}
if: ${{ startsWith(github.ref, 'refs/tags/') }}
if: ${{ always() }}             # Run even if previous failed
if: ${{ failure() }}            # Run only if failed
if: ${{ success() }}            # Run only if succeeded
```

## Common Workflows

### Node.js CI
```yaml
name: Node.js CI
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm run build
```

### Deploy on Release
```yaml
name: Deploy
on:
  release:
    types: [published]
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/checkout@v4
      - run: ./deploy.sh
        env:
          DEPLOY_TOKEN: ${{ secrets.DEPLOY_TOKEN }}
```

### PR Checks
```yaml
name: PR Checks
on: pull_request
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm run lint
        
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm test
```

## Reusable Workflows

### Define Reusable
```yaml
# .github/workflows/reusable-deploy.yml
name: Reusable Deploy
on:
  workflow_call:
    inputs:
      environment:
        required: true
        type: string
    secrets:
      deploy_token:
        required: true
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: ${{ inputs.environment }}
    steps:
      - run: ./deploy.sh
```

### Call Reusable
```yaml
jobs:
  deploy:
    uses: ./.github/workflows/reusable-deploy.yml
    with:
      environment: production
    secrets:
      deploy_token: ${{ secrets.DEPLOY_TOKEN }}
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Secret not available | Check secret name, scope |
| Cache not working | Verify key, check paths |
| Job skipped | Check `if` conditions |
| Permission denied | Check `permissions` block |
| Timeout | Increase `timeout-minutes` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tjboudreaux) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
