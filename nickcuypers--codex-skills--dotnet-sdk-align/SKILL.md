---
name: dotnet-sdk-align
description: Align .NET SDK pinning (`global.json` + CI references) and validate restore/build/test impact in a gated workflow. Use when this capability is needed.
metadata:
  author: nickcuypers
---

## When to use / when NOT to use
- Use to standardize SDK versions and avoid local/CI drift.
- Do not use for package-version upgrades (use package workflow skill).

## Preconditions (tools, versions, repo state)
- `dotnet` CLI installed.
- Repo contains .NET files (`*.sln`, `*.csproj`, or props files).
- Clean git state before `--execute`.

## Workflow (DISCOVER → PLAN → EXECUTE → VERIFY → REPORT)
1. DISCOVER: inspect SDK pinning and CI hints.
2. PLAN: define target SDK and expected impact.
3. EXECUTE: update/create `global.json` for target SDK.
4. VERIFY: run restore/build/test.
5. REPORT: summarize alignment status and remaining CI actions.

## Exact commands and expected signals
```bash
skills/dotnet-sdk-align/scripts/run.sh --dry-run
skills/dotnet-sdk-align/scripts/run.sh --execute --sdk=8.0.204 --ci
skills/dotnet-sdk-align/scripts/run.sh --verify-only --ci
```
Success: `global.json` reflects target SDK (execute) and restore/build/test complete.
Failure: missing `dotnet`, dirty tree, or omitted `--sdk` in execute mode.

## If it fails (checklist)
- Install required .NET SDK locally.
- Verify SDK version exists (`dotnet --list-sdks`).
- Reconcile CI image/toolset with selected SDK.

## Final report template
- Current vs target SDK.
- Files touched (`global.json`, CI references).
- Restore/build/test results.
- CI changes needed.
- Rollback command.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nickcuypers) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
