---
name: git-sync
description: Automatically syncs local workspace changes to the remote GitHub repository. Use after significant changes or periodically. Use when this capability is needed.
metadata:
  author: aztr0nutzs
---

# Git Sync Skill

Automatically syncs local workspace changes to the remote GitHub repository.
Designed to be called by PCEC cycles or after significant changes.

## Tools

### git_sync
Commit and push changes.

- **message** (optional): Commit message. Defaults to "Auto-sync: Routine evolution update".

## Safety
- Uses `.gitignore` and `pre-commit` hooks (ADL-compliant) to prevent secret leakage.
- Checks if there are changes before committing.

## Implementation
Wrapper around `git add . && git commit && git push`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aztr0nutzs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
