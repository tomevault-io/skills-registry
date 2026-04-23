---
name: gh-issue-sync
description: Create or update GitHub issue for the story and sub-issues for tasks Use when this capability is needed.
metadata:
  author: macroman5
---

# GitHub Issue Sync

## Purpose
Keep GitHub issues in sync with local USER-STORY and TASK files.

## Behavior
1. Draft issue titles/bodies from local files (story + tasks).
2. Propose labels and links (paths, story ID, task IDs).
3. Output GitHub CLI commands (dry-run by default); confirm before executing.

## Guardrails
- Do not post without explicit confirmation.
- Reflect exactly what exists on disk; no invented tasks.

## Integration
- After `/lazy create-feature` creates files; optional during `/lazy story-review`.

## Example Prompt
> Prepare GitHub issues for US-20251027-001 and its tasks (dry run).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/macroman5) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
