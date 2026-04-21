---
name: session-start
description: Initialize a new work session. Creates brain backup, validates environment, and prepares for planning. Use at the beginning of any development session. Use when this capability is needed.
metadata:
  author: choka30
---

# Session Start

Initialize a new work session with proper state management.

## Execution Protocol

### Step 1: Create Brain Backup
```bash
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR=".brain-backups/${TIMESTAMP}"
mkdir -p "${BACKUP_DIR}"
cp -r brain/* "${BACKUP_DIR}/"
echo "✓ Brain backup created: ${BACKUP_DIR}"
```

### Step 2: Validate Environment
- [ ] Confirm in git repository
- [ ] Check current branch (should be feature branch or worktree)
- [ ] Verify brain/ files exist
- [ ] Confirm no uncommitted changes (or stash them)

### Step 3: Load Context
Read these files to understand current state:
1. `brain/development_standard.md` — Standards to follow
2. `brain/general_index.md` — Project structure
3. `brain/history_log.md` (last 2 entries) — Recent context

### Step 4: Output Session Header
```
═══════════════════════════════════════════════════════
  SESSION INITIALIZED
  Date: [current date/time]
  Branch: [current branch]
  Backup: [backup path]
═══════════════════════════════════════════════════════

Ready for work request. Provide a feature description to begin planning.

Next step: /plan "your feature request"
```

## Session Rules
- Each session targets 1-2 hours of work
- Maximum 6 atomic tasks per session
- All tasks must be in plan.md before execution
- End session with `/session-end` before merging

## Failure Recovery
If session-start fails:
1. Check `.brain-backups/` for recent backups
2. Verify brain/ directory integrity
3. Run `git status` to check repository state

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/choka30) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
