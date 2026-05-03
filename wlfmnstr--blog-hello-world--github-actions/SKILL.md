---
name: github-actions
description: GitHub Actions workflow patterns for deployment. Use when creating CI/CD workflows. Use when this capability is needed.
metadata:
  author: wlfmnstr
---

# GitHub Actions Skill

## Deploy to GitHub Pages Pattern

**File: `.github/workflows/deploy.yml`**

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Build
        run: |
          npm run tina:build
          npm run build
          npx pagefind --site dist

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./dist

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## Key Points

- Two jobs: build and deploy
- Pagefind runs after Astro build
- Uses official GitHub Pages actions
- Requires proper permissions

## GitHub Pages Setup

1. Repository Settings → Pages
2. Source: GitHub Actions (not branch)
3. First push triggers deployment

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wlfmnstr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
