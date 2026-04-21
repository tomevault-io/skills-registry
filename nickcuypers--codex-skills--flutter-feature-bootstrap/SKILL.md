---
name: flutter-feature-bootstrap
description: Scaffold a new Flutter feature module (folders + starter files + tests) to remove repetitive manual project setup work. Use when this capability is needed.
metadata:
  author: nickcuypers
---

## When to use / when NOT to use
- Use when starting a new feature and you want consistent scaffolding.
- Do not use when repository has a custom architecture template that differs.

## Preconditions (tools, versions, repo state)
- Flutter repo with `pubspec.yaml`.
- Feature name provided via `--feature`.
- Clean tree for `--execute`.

## Workflow (DISCOVER → PLAN → EXECUTE → VERIFY → REPORT)
1. DISCOVER: confirm project and feature target.
2. PLAN: preview directories/files to generate.
3. EXECUTE: create feature structure and test stubs.
4. VERIFY: list generated artifacts.
5. REPORT: summarize what was scaffolded.

## Exact commands and expected signals
```bash
skills/flutter-feature-bootstrap/scripts/run.sh --dry-run --feature=payments
skills/flutter-feature-bootstrap/scripts/run.sh --execute --feature=payments
skills/flutter-feature-bootstrap/scripts/run.sh --verify-only --feature=payments
```
Success: feature folder and starter files created in execute mode.
Failure: missing `--feature`, dirty tree, or invalid project context.

## If it fails (checklist)
- Provide a valid kebab/snake feature name.
- Ensure git working tree is clean.
- Check write permissions.

## Final report template
- Feature requested.
- Files/folders created.
- Follow-up manual wiring steps.
- Rollback command.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nickcuypers) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
