---
name: github-actions
description: Set up GitHub Actions CI for lint, typecheck, and tests. Use when this capability is needed.
metadata:
  author: north-brook
---

# GitHub Actions

## Setup

Create `.github/workflows/ci.yml` in the project root. See `references/ci.yml` for the full template.

The CI pipeline mirrors the Husky pre-commit checks:
1. **Lint** — `bunx biome ci .`
2. **Typecheck** — bun run tsc --noEmit
3. **Test** — `bunx playwright test`

## Triggers

- Push to `main`
- All pull requests

## Key Points

- Use `oven-sh/setup-bun@v2` for bun.
- Use `bun install --frozen-lockfile` for reproducible installs.
- Install Playwright browsers with `bunx playwright install --with-deps`.
- Cache bun store: `~/.bun/install/cache`.
- Set `CI=true` environment variable.

## Adding the Workflow

```bash
mkdir -p .github/workflows
# Copy from references/ci.yml or write directly
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/north-brook) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
