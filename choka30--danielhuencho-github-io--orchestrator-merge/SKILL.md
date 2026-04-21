---
name: orchestrator-merge
description: Coordinate merging of multiple worktree branches into main. Handles brain file reconciliation, conflict resolution, and cleanup. Use after parallel agents complete their sessions. Use when this capability is needed.
metadata:
  author: choka30
---

# Orchestrator Merge

Coordinate multi-worktree merging into main branch.

## Prerequisites

Before running orchestrator-merge:
- [ ] All worktree sessions ended (`/session-end`)
- [ ] All worktree branches pushed to remote
- [ ] All tests passing in each worktree
- [ ] You are in the **main project directory** (not a worktree)

## Execution Protocol

### Step 1: Discovery Phase

```bash
echo "Scanning worktrees..."
git worktree list

echo ""
echo "Branches ready for merge:"
for worktree in $(git worktree list --porcelain | grep "worktree" | cut -d' ' -f2); do
    if [ "$worktree" != "$(pwd)" ]; then
        branch=$(git -C "$worktree" branch --show-current)
        commits=$(git rev-list --count main..$branch 2>/dev/null || echo "?")
        echo "  - $branch ($commits commits) @ $worktree"
    fi
done
```

**Output:**
```
Found worktrees:
─────────────────────────────────────────────────────────
1. feature/auth     (+120 lines, 5 commits)  ../project-auth
2. feature/api      (+200 lines, 8 commits)  ../project-api
3. main             (base)                   ./
─────────────────────────────────────────────────────────
```

### Step 2: Conflict Analysis

For each branch pair, analyze potential conflicts:

```bash
# Check for overlapping files
git diff --name-only main..feature/auth > /tmp/auth_files.txt
git diff --name-only main..feature/api > /tmp/api_files.txt
comm -12 <(sort /tmp/auth_files.txt) <(sort /tmp/api_files.txt)
```

**Conflict Categories:**

| Type | Risk | Resolution Strategy |
|------|------|---------------------|
| No overlap | Low | Direct merge |
| Same file, different sections | Medium | Auto-merge likely |
| Same file, same lines | High | Manual resolution required |
| Brain file conflicts | Medium | Special merge rules apply |

### Step 3: Generate Merge Plan

```
═══════════════════════════════════════════════════════
  MERGE PLAN
═══════════════════════════════════════════════════════

Phase 1: Safe Merges (no conflicts)
  1. Merge feature/auth → main

Phase 2: Conflicting Merges
  2. Merge feature/api → main
     ⚠ Conflict: src/api/routes.py (lines 45-60)
     Strategy: Keep both route registrations

Phase 3: Brain Reconciliation
  3. Merge brain/ files using brain merge rules

Phase 4: Verification
  4. Run full test suite
  5. Update brain/ indexes

Phase 5: Cleanup
  6. Remove merged worktrees

─────────────────────────────────────────────────────────
Proceed with merge plan? (yes/modify/abort)
```

### Step 4: Execute Merges

**For each branch (in order):**

```bash
# Checkout main
git checkout main
git pull origin main

# Merge branch
git merge feature/auth --no-ff -m "Merge feature/auth: [description]"

# If conflict:
#   1. Show conflicting files
#   2. Present conflict to human
#   3. Await resolution
#   4. git add <resolved_files>
#   5. git commit
```

### Step 5: Brain File Reconciliation

**Special merge rules for brain/ files:**

#### plan.md
```bash
# Reset to template (worktree plans are session-specific)
git checkout main -- brain/plan.md
# Or keep template version - plans are transient
```

#### general_index.md
```bash
# Union of all structural changes
# Merge all "Recent Changes" entries
# Update directory tree to reflect merged state
```

#### codebase_index.md
```bash
# Union of all new entries
# Deduplicate identical signatures
# Keep most recent description if conflict
```

#### development_standard.md
```bash
# Should not have changed (read-only in worktrees)
# If changed, keep main version and flag for review
```

#### history_log.md
```bash
# Concatenate all session entries chronologically
# Sort by date
# No deduplication (each session is unique)
```

### Step 6: Post-Merge Verification

```bash
# Run full test suite
poetry run pytest tests/ -v --cov=src

# Verify brain consistency
echo "Checking brain files..."
ls -la brain/
```

### Step 7: Cleanup Worktrees

```bash
# For each merged worktree
git worktree remove ../project-auth
git branch -d feature/auth

# Verify cleanup
git worktree list
git branch -a
```

## Output Format

### Success
```
═══════════════════════════════════════════════════════
  ✓ ORCHESTRATOR MERGE COMPLETE
═══════════════════════════════════════════════════════

Merged Branches:
  ✓ feature/auth → main (5 commits)
  ✓ feature/api → main (8 commits)

Brain Reconciliation:
  ✓ general_index.md - merged structural changes
  ✓ codebase_index.md - merged 12 new entries
  ✓ history_log.md - appended 2 session logs
  ○ plan.md - reset to template
  ○ development_standard.md - unchanged

Tests: ✓ All passing (67/67)
Coverage: 86%

Cleanup:
  ✓ Removed worktree: ../project-auth
  ✓ Removed worktree: ../project-api
  ✓ Deleted branches: feature/auth, feature/api

Main branch ready for next sprint.
```

### Conflict Resolution Required
```
═══════════════════════════════════════════════════════
  ⚠️ CONFLICT DETECTED
═══════════════════════════════════════════════════════

File: src/api/routes.py
Branch: feature/api

<<<<<<< HEAD (main)
router.include_router(auth_router, prefix="/auth")
=======
router.include_router(auth_router, prefix="/v1/auth")
router.include_router(users_router, prefix="/v1/users")
>>>>>>> feature/api

Options:
[A] Keep main version
[B] Keep feature/api version
[C] Keep both (manual edit)
[D] Open in editor

Selection: 
```

## Worktree Naming Convention

Standardized pattern for worktrees:
```
../project-{feature-name}

Examples:
  ../project-auth
  ../project-data-pipeline
  ../project-api-v2
```

## Creating New Worktrees

For reference (used before sessions, not during merge):

```bash
# Create new worktree for feature
git worktree add ../project-{feature} -b feature/{feature}

# Navigate and start session
cd ../project-{feature}
claude
# Then run /session-start
```

## Notes

- Always merge from main directory, not worktrees
- Merge order matters: least conflicts first
- Brain files have special merge rules
- Tests must pass after each merge step
- Keep worktrees until merge verified
- history_log.md is append-only, never loses data

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/choka30) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
