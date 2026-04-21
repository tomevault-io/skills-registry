---
name: flutter-release-readiness
description: Pre-release Flutter gate that runs deterministic checks for version/changelog hygiene, analysis/tests, and platform build smoke verification. Use when this capability is needed.
metadata:
  author: nickcuypers
---

## When to use / when NOT to use
- Use before cutting a release branch/tag.
- Do not use for day-to-day dependency updates.

## Preconditions (tools, versions, repo state)
- Flutter toolchain installed.
- `pubspec.yaml` in repo root.
- Clean working tree for `--execute`.

## Workflow (DISCOVER → PLAN → EXECUTE → VERIFY → REPORT)
1. DISCOVER: detect Flutter project and release metadata files.
2. PLAN: build pre-release checklist.
3. EXECUTE: enforce clean state and prepare checks.
4. VERIFY: run analyze/test/build smoke commands.
5. REPORT: summarize go/no-go outcomes.

## Exact commands and expected signals
```bash
skills/flutter-release-readiness/scripts/run.sh --dry-run
skills/flutter-release-readiness/scripts/run.sh --verify-only --ci
skills/flutter-release-readiness/scripts/run.sh --execute --ci
```
Success: report generated and verification checks complete.
Failure: missing toolchain/project or failing checks.

## If it fails (checklist)
- Run `flutter doctor` and fix setup.
- Resolve analyze/test failures before release.
- Re-run with `--verbose`.

## Final report template
- Release scope and version/changelog status.
- Verification command outcomes.
- Blocking issues.
- Next actions and rollback command.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nickcuypers) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
