---
name: dev-work-summary
description: Scan ~/dev recursively for git repos and report today's work with commits, branches, stats, and detailed change analysis. Use when user asks "what did I work on", "show my work", "daily summary", "what repos did I touch". Use when this capability is needed.
metadata:
  author: otrebu
---

# Dev Work Summary

## Overview

Scans all git repos in ~/dev and reports today's activity with commit messages, branch info, file changes, and detailed analysis.

## Usage

Invoke when user wants to review their work:

- "What did I work on today?"
- "Show me my daily activity"
- "Which repos did I touch?"
- "Summarize my work"

## Workflow

1. Run scan script:

   ```bash
   bash .claude/skills/dev-work-summary/scripts/scan-repos.sh
   ```

2. Script outputs:

   - Repo name and path
   - Current branch
   - Uncommitted changes (if any)
   - Today's commits (messages, timestamps)
   - Stats (files/lines changed)
   - File-level changes (added/modified/deleted)

3. Analyze output and summarize for user:
   - Group by project/theme
   - Highlight key accomplishments
   - Note incomplete work (uncommitted changes)
   - Identify cross-repo patterns

## Report Structure

Per repo with activity:

- 📁 Repo name and location
- 🌿 Current branch
- ⚠️ Uncommitted changes (git status)
- 📝 Commit list (hash, message, time)
- 📊 Aggregated stats
- ✅ Files added
- ✏️ Files modified
- ❌ Files deleted

## Notes

- "Today" = since midnight (00:00:00)
- Recursive scan finds nested repos
- Skips repos with no activity today
- Shows total repos scanned at end

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/otrebu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
