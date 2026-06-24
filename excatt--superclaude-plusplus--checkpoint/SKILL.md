---
name: checkpoint
description: Save and restore work state with Git-based lightweight checkpoint system. Use before risky work, milestone completion, experimental changes. Keywords: checkpoint, save, restore, rollback, snapshot, backup, git, stash. Use when this capability is needed.
metadata:
  author: excatt
---

# Checkpoint Skill

## Purpose
Save, compare, and restore work state when necessary using Git stash/commit-based lightweight checkpoint system.

**Core Principle**: Always checkpoint before risky work → Safe experimentation → Rollback if needed

## Activation Triggers
- Before risky refactoring
- Before starting experimental changes
- On major milestone completion
- Before PR submission (save final state)
- User explicit request: `/checkpoint`, `checkpoint`, `save`

---

## Commands

### Create Checkpoint
```
/checkpoint create <name>
```

**Actions**:
1. Verify current state is clean
2. Create stash or commit if changes exist
3. Record in `.claude/checkpoints.log`
4. Save timestamp and Git SHA

**Example**:
```
/checkpoint create before-auth-refactor

✅ Checkpoint Created
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Name:      before-auth-refactor
Time:      2025-01-26 14:30:22
SHA:       a1b2c3d
Files:     12 staged, 3 modified
Status:    Clean state saved
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

### Verify Against Checkpoint
```
/checkpoint verify <name>
```

**Actions**:
1. Find saved checkpoint
2. Compare with current state
3. Report change metrics

**Example**:
```
/checkpoint verify before-auth-refactor

📊 Checkpoint Comparison
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Checkpoint:    before-auth-refactor
Created:       2025-01-26 14:30:22
Current SHA:   d4e5f6g

📁 Files Changed: 8
   ├─ src/auth/login.ts      (+45, -12)
   ├─ src/auth/middleware.ts (+23, -5)
   ├─ src/api/user.ts        (+15, -8)
   └─ ... 5 more files

📈 Test Coverage:
   Before: 78%
   After:  85% (+7%)

🧪 Tests:
   Before: 42 passed
   After:  47 passed (+5 new)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

### List Checkpoints
```
/checkpoint list
```

**Example**:
```
/checkpoint list

📋 Saved Checkpoints
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Name                    Time                 SHA       Status
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
before-auth-refactor    2025-01-26 14:30    a1b2c3d   ← HEAD~3
feature-complete        2025-01-26 12:15    x7y8z9a   ← HEAD~8
initial-setup           2025-01-26 09:00    m4n5o6p   ← HEAD~15
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Total: 3 checkpoints
```

---

### Restore Checkpoint
```
/checkpoint restore <name>
```

**Actions**:
1. Backup current changes (auto stash)
2. Restore to checkpoint
3. Confirm restoration

**Example**:
```
/checkpoint restore before-auth-refactor

⚠️ Restore Confirmation
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
This will:
1. Stash current changes (auto-backup)
2. Reset to checkpoint: before-auth-refactor
3. You will lose: 8 file changes since checkpoint

Proceed? [y/N]
```

---

### Clear Old Checkpoints
```
/checkpoint clear
```

**Action**: Keep only the most recent 5 checkpoints, delete the rest

---

## Implementation Details

### Checkpoint Log Format
`.claude/checkpoints.log`:
```
[2025-01-26T14:30:22] before-auth-refactor | SHA:a1b2c3d | stash@{0}
[2025-01-26T12:15:10] feature-complete | SHA:x7y8z9a | commit
[2025-01-26T09:00:05] initial-setup | SHA:m4n5o6p | commit
```

### Git Commands Used
```bash
# Create checkpoint (commit-based)
git add -A
git commit -m "checkpoint: <name>"

# Create checkpoint (stash-based for uncommitted work)
git stash push -m "checkpoint: <name>"

# Verify
git diff <checkpoint-sha>..HEAD --stat

# Restore
git stash  # backup current
git checkout <checkpoint-sha>

# List
git log --oneline --grep="checkpoint:"
```

---

## Workflow Integration

### Typical Development Flow
```
/checkpoint create "start-feature"
    │
    ├─→ Implementation work
    │
    ├─→ /verify quick
    │
    ├─→ /checkpoint create "mid-feature"
    │
    ├─→ Risky refactoring
    │       │
    │       ├─→ Success → Continue
    │       │
    │       └─→ Failure → /checkpoint restore "mid-feature"
    │
    ├─→ /verify full
    │
    └─→ /checkpoint create "feature-complete"
```

### With `/verify`
```
/checkpoint create "before-refactor"
... refactoring ...
/verify full
    │
    ├─→ ✅ Pass → Continue
    │
    └─→ ❌ Fail → /checkpoint restore "before-refactor"
```

### With `/feature-planner`
Auto-checkpoint before each Phase:
```
Phase 1 start → /checkpoint create "phase-1-start"
Phase 1 complete → /checkpoint create "phase-1-complete"
Phase 2 start → /checkpoint create "phase-2-start"
...
```

---

## Best Practices

### Checkpoint Naming Rules
```
✅ Good:
- before-auth-refactor
- phase-2-complete
- pre-db-migration
- working-login-flow

❌ Bad:
- checkpoint1
- test
- temp
- asdf
```

### Recommended Checkpoint Timing
| Situation | Checkpoint Name |
|-----------|----------------|
| Feature start | `start-<feature>` |
| Reached working state | `working-<feature>` |
| Before risky change | `before-<change>` |
| Phase completion | `phase-N-complete` |
| Before PR | `pre-pr-<feature>` |

### Checkpoint Management
- Run `/checkpoint clear` when 5+ accumulate
- Use meaningful names
- Create checkpoints only in working states

---

## Quick Reference

| Command | Description |
|---------|-------------|
| `/checkpoint create <name>` | Create checkpoint |
| `/checkpoint verify <name>` | Compare with checkpoint |
| `/checkpoint list` | List all checkpoints |
| `/checkpoint restore <name>` | Restore to checkpoint |
| `/checkpoint clear` | Clean old checkpoints |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/excatt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
