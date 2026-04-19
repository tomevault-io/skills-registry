---
name: check-deploy
description: Run pre-deploy checks (types, tests, build) Use when this capability is needed.
metadata:
  author: vincentgagnondev
---

# Pre-deploy Check

Run all validation steps before deploying to Vercel. Report results for each step. If any step fails, stop and show the errors.

## Steps

1. **TypeScript type check** — `npx tsc -b`
2. **Run tests** — `npm run test`
3. **Production build** — `npm run build`

## On Success

Report all three steps passed and confirm the app is ready to deploy.

## On Failure

Stop at the first failing step. Show the full error output and suggest fixes. Do not proceed to later steps — a type error means the build will also fail.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vincentgagnondev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
