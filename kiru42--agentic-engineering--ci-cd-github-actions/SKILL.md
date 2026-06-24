---
name: ci-cd-github-actions
description: GitHub Actions patterns for CI (lint/typecheck/test) and Release (semver/changelog) with Node 20/22 matrix, pnpm cache, reusable workflows, and OIDC cloud authentication. Use when this capability is needed.
metadata:
  author: kiru42
---

# GitHub Actions CI/CD

## Core Principle

Two distinct workflows: CI for quality gates, Release for deployments.

```
.github/workflows/
├── ci.yml          # Pull request quality gates
├── release.yml     # Release + deploy on tag
└── reusable/       # Shared workflow templates
    ├── lint.yml
    ├── test.yml
    └── build.yml
```

## CI Workflow (ci.yml)

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  lint:
    uses: ./.github/workflows/reusable/lint.yml
    with:
      pnpm-version: '9'

  typecheck:
    uses: ./.github/workflows/reusable/typecheck.yml
    with:
      node-version: '22'

  test:
    strategy:
      matrix:
        node: ['20', '22']
    uses: ./.github/workflows/reusable/test.yml
    with:
      node-version: ${{ matrix.node }}
```

## Reusable Lint Workflow

```yaml
# .github/workflows/reusable/lint.yml
on:
  workflow_call:
    inputs:
      pnpm-version:
        required: false
        type: string
        default: '9'

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
        with:
          version: ${{ inputs.pnpm-version }}
      - uses: actions/setup-node@v4
        with:
          node-version: '22'
          cache: 'pnpm'
      - run: pnpm install --frozen-lockfile
      - run: pnpm lint
      - run: pnpm typecheck
```

## Release Workflow

```yaml
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    permissions:
      id-token: write  # For OIDC
      contents: write  # For release
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: actions/setup-node@v4
        with:
          node-version: '22'
          cache: 'pnpm'
      - run: pnpm install --frozen-lockfile
      - run: pnpm build
      
      # Generate changelog
      - name: Generate Changelog
        run: |
          npx conventional-changelog-cli -p angular -i CHANGELOG.md -s
          git config user.name "GitHub Actions"
          git config user.email "actions@github.com"
          git add CHANGELOG.md
          
      # Create GitHub Release
      - name: Release
        uses: softprops/action-gh-release@v2
        with:
          generate_release_notes: true
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## OIDC for Cloud (AWS Example)

```yaml
- name: Configure AWS Credentials
  uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: arn:aws:iam::123456789:role/github-actions
    aws-region: eu-west-1
```

## References

- `references/workflows.md` - Detailed workflow patterns
- `references/oidc.md` - OIDC setup for cloud providers

---
> Source: [kiru42/agentic-engineering](https://github.com/kiru42/agentic-engineering) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
