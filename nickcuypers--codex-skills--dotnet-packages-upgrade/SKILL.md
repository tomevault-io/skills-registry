---
name: dotnet-packages-upgrade
description: Safe NuGet upgrade workflow for SDK-style repos with central package management awareness and restore/build/test gates. Use when this capability is needed.
metadata:
  author: nickcuypers
---

## When to use / when NOT to use
- Use for NuGet hygiene and controlled upgrade planning/execution.
- Do not use for SDK pinning changes (use `dotnet-sdk-align`).

## Preconditions (tools, versions, repo state)
- `dotnet` CLI installed.
- Repo has .NET projects and optional central package management files.

## Workflow (DISCOVER → PLAN → EXECUTE → VERIFY → REPORT)
1. DISCOVER: locate version-definition files (`Directory.Packages.props`, `*.csproj`, lockfiles).
2. PLAN: define package bump slices based on outdated output.
3. EXECUTE: restore and generate upgrade candidates (manual targeted bump step).
4. VERIFY: restore/build/test + outdated re-check.
5. REPORT: summarize safe next edits for review.

## Exact commands and expected signals
```bash
skills/dotnet-packages-upgrade/scripts/run.sh --dry-run
skills/dotnet-packages-upgrade/scripts/run.sh --execute --ci
skills/dotnet-packages-upgrade/scripts/run.sh --verify-only --ci
```
Success: outdated inventory and verification output produced.
Failure: missing `dotnet` or build/test failures after upgrades.

## If it fails (checklist)
- Confirm SDK/runtime compatibility.
- Run restore with detailed logs.
- Verify central package versions are coherent.

## Final report template
- Version-definition locations discovered.
- Outdated package summary.
- Restore/build/test/analyze outcomes.
- Recommended package bump PR chunks.
- Rollback command.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nickcuypers) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
