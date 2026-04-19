---
name: build-check
description: Verify that all project services (API and Web) build successfully. Check compilation errors, type errors, and lint warnings. Use before running tests, before deployment, after code changes, and on resume. Trigger when build verification, compilation check, or pre-test validation is needed. Use when this capability is needed.
metadata:
  author: crgarcia12
---

# Build Check

Verify builds succeed before proceeding with tests or deployment.

## Build Commands

| Service | Source Build | Docker Build |
|---------|------------|--------------|
| API (.NET) | `cd src/api && dotnet build` | `docker build -f src/api/Dockerfile .` |
| Web (Next.js) | `cd src/web && npm run build` | `docker build -f src/web/Dockerfile .` |

## Steps

1. **Identify services** — Read `azure.yaml` or scan `src/` for service directories
2. **Build each service** — Run the source build command for each service
3. **Capture errors** — Collect compilation errors, type errors, and warnings
4. **Docker build** (if preparing for deployment) — Run Docker build for each Dockerfile
5. **Report** — Summarize build status per service

## Output Format

```
Service: <name>
Status: PASS | FAIL
Errors: <count>
Warnings: <count>

Details:
- <file>:<line> <error message>
```

## Notes

- Web (Next.js) requires `output: 'standalone'` in `next.config.ts`
- API uses .NET 10 Minimal API

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/crgarcia12) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
