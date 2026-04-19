---
name: ci
description: Check CI status, view workflow runs, and inspect logs for the current git branch. Use when user types /ci or asks about CI, build status, workflow runs, or GitHub Actions. Use when this capability is needed.
metadata:
  author: aviralmansingka
---

# CI Status Interaction

Interact with GitHub Actions CI for the current branch using the `gh` CLI.

## Instructions

1. **Get current branch**: `git branch --show-current`

2. **Based on user request, run the appropriate command**:

| Action | Command |
|--------|---------|
| Check CI status | `gh run list --branch <branch> --limit 5` |
| View latest run details | `gh run view --branch <branch>` |
| View specific run | `gh run view <run-id>` |
| View failed jobs | `gh run view <run-id> --log-failed` |
| View full logs | `gh run view <run-id> --log` |
| Watch run in progress | `gh run watch` |
| Re-run failed jobs | `gh run rerun <run-id> --failed` |
| Re-run entire workflow | `gh run rerun <run-id>` |
| List workflows | `gh workflow list` |

3. **Default behavior** (when user just types `/ci`):
   - Get current branch
   - Run `gh run list --branch <branch> --limit 5` to show recent runs
   - If there's a failing or in-progress run, show details with `gh run view`

4. **For failures**: Automatically fetch failed job logs with `--log-failed` to help diagnose issues.

## Examples

- `/ci` -> Show CI status for current branch
- `/ci logs` -> Show logs for the latest run
- `/ci rerun` -> Re-run failed jobs from the latest run
- `/ci watch` -> Watch the current run in progress

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aviralmansingka) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
