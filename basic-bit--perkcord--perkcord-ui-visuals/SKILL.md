---
name: perkcord-ui-visuals
description: Perkcord UI visual confidence loop (Playwright + Storybook) Use when this capability is needed.
metadata:
  author: basic-bit
---

# Perkcord UI Visuals Skill

Purpose: make UI changes safely with a deterministic visual verification loop.

## When To Use

- You changed anything user-facing in `apps/web` (copy, layout, component styling, subscribe/admin flows).
- You updated branding/theming, tier rendering, checkout UI, or admin dashboards.

## Guardrails

- Treat visual snapshots as a contract: update them only when the change is intentional.
- Prefer stable test data (seeded) and stable page states (wait for navigation/data before screenshot).
- If a snapshot changes unexpectedly, investigate before updating.

## Commands

Core web checks:

```bash
npm --prefix apps/web run lint
npm --prefix apps/web run typecheck
npm --prefix apps/web run test
```

Playwright visuals:

```bash
PLAYWRIGHT_TEST_PORT=3002 npm --prefix apps/web run test:e2e:visual
```

If snapshots must be updated intentionally:

```bash
PLAYWRIGHT_TEST_PORT=3002 npm --prefix apps/web run test:e2e:visual:update
```

If you need Linux-rendered Playwright baselines locally:

```bash
bash scripts/update-linux-snapshots.sh --playwright-only
```

Storybook snapshots:

```bash
npm --prefix apps/web run test:storybook
```

If Storybook snapshots must be updated intentionally:

```bash
npm --prefix apps/web run test:storybook:update
```

## Snapshot Locations

- Playwright: `apps/web/e2e/*.spec.ts-snapshots/`
- Storybook: `apps/web/storybook-snapshots/`

Notes:

- CI runs on Linux, so snapshot baselines are platform-specific.
  - Playwright snapshots include the OS in the filename (e.g. `*-chromium-linux.png`).
  - Storybook snapshots include the platform suffix (e.g. `*-linux.png`, `*-win32.png`).

## Troubleshooting Notes

- If Playwright hangs or ports are busy, check `3210/3211` (Convex) and `3001/3002` (Next).
- If admin pages show `Unauthorized.` or `API key not configured.`, check `PERKCORD_REST_API_KEY` in both Vercel and Convex.
- On Windows, if Linux snapshot refresh fails because Docker is not running, start Docker Desktop first (for example via PowerShell `Start-Process "$Env:ProgramFiles\Docker\Docker\Docker Desktop.exe"`) and rerun `bash scripts/update-linux-snapshots.sh --playwright-only`.
- Local Playwright runs now auto-strip repo-root cloud Convex keys before booting local Convex; if a run still targets cloud unexpectedly, inspect repo-root `.env.local`, `apps/web/.env.local`, and `convex/.env.local` for conflicting overrides.
- If Storybook output looks unstyled, confirm Tailwind is applied (PostCSS pipeline in `apps/web/.storybook/main.ts`).

## Human Review (VLM)

When snapshots change:

- Review the PNG diffs with a VLM before declaring the work done.
- Record the intent in the PR description: what changed and why.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/basic-bit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
