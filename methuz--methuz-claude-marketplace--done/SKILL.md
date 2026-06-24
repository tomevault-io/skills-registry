---
name: done
description: Complete work on a ClickUp task - merge, comment, and update status Use when this capability is needed.
metadata:
  author: methuz
---

# /clickup:done - Complete Task

## Purpose
Complete development on the current ClickUp task by bumping version, merging to staging, adding a summary comment, and updating the task status to "To Review".

## Usage
```
/clickup:done                    # Auto-detect task from current branch
/clickup:done <task_id>          # Explicitly specify task ID
/clickup:done --keep-worktree    # Don't remove worktree after completion
/clickup:done --no-bump          # Skip version bump
/clickup:done --no-changelog     # Skip changelog update
/clickup:done --minor            # Force minor version bump
/clickup:done --major            # Force major version bump
```

## Options
- `<task_id>`: Explicitly specify the ClickUp task ID (overrides auto-detection)
- `--keep-worktree`: Don't remove the worktree after completion
- `--no-bump`: Skip version bump and changelog update
- `--no-changelog`: Update version but skip changelog
- `--minor`: Force minor version bump (override auto-detection)
- `--major`: Force major version bump (override auto-detection)

## Behavioral Flow

When this command is invoked:

1. **Detect Current Task**:
   - **If task_id provided**: Use the explicit task ID
   - **If no task_id**: Auto-detect from current branch:
     ```bash
     # Get current branch
     BRANCH=$(git rev-parse --abbrev-ref HEAD)

     # Extract task ID from branch pattern: {type}/{task_id}-{slug}
     # Examples: fix/86bqy2f6j-payment-webhook → 86bqy2f6j
     #           feature/abc123-user-profile → abc123
     TASK_ID=$(echo "$BRANCH" | sed -E 's|^(fix|feature|task)/([^-]+)-.*|\2|')
     ```
   - Validate task exists in ClickUp
   - **Error if**: Not on a task branch AND no task_id provided

2. **Check Working Directory**:
   - Check for uncommitted changes: `git status --porcelain`
   - If changes exist, prompt to commit or stash

3. **Generate Summary**:
   Gather information for the completion comment:
   ```bash
   # Commits on this branch
   git log --oneline staging..HEAD

   # Files changed
   git diff --stat staging...HEAD

   # Diff summary
   git diff --shortstat staging...HEAD
   ```

4. **Version Bump** (unless --no-bump):
   Determine version bump type based on branch prefix:

   | Branch Prefix | Version Bump | Example |
   |---------------|--------------|---------|
   | `feature/` | minor | 0.1.1 → 0.2.0 |
   | `feat/` | minor | 0.1.1 → 0.2.0 |
   | `fix/` | patch | 0.1.1 → 0.1.2 |
   | `task/` | patch | 0.1.1 → 0.1.2 |
   | `refactor/` | patch | 0.1.1 → 0.1.2 |
   | `chore/` | patch | 0.1.1 → 0.1.2 |

   Steps:
   a. Read current version from `package.json`
   b. Calculate new version based on branch type (or flag override)
   c. Update `package.json` with new version
   d. Update changelog (unless --no-changelog):
      - For `src/routes/changelog/+page.svelte`: Add entry to changelog array
      - For `CHANGELOG.md`: Prepend new section
      - Map types: feature/feat→feature, fix→fix, others→improvement
   e. Commit version bump:
      ```bash
      git add package.json
      git add [changelog file if exists]
      git commit -m "chore: bump version to X.Y.Z"
      ```

5. **Show Preview**:
   ```
   📋 Completing task: Fix payment webhook race condition

   📦 Version bump: 0.7.3 → 0.7.4 (patch)

   📝 Summary of changes:
     - 3 commits on branch
     - 4 files changed (+127 -23)

   Generated comment:
   ---
   ## Development Complete 🚀

   **Branch**: fix/86bqy2f6j-payment-webhook-race
   **Version**: 0.7.4
   **Merged to**: staging

   ### Commits
   - fix: add mutex lock for webhook processing
   - test: add concurrent webhook tests
   - docs: update webhook handling notes

   ### Files Changed
   - src/routes/api/webhooks/+server.ts (+45 -12)
   - src/lib/server/webhookHandler.ts (+62 -8)
   - tests/webhooks.spec.ts (+20 -3)
   - README.md (+5 -0)
   ---

   Proceed? [Y/n]
   ```

6. **Execute Git Operations** (Local Only):
   ```bash
   # Navigate to main project directory
   cd /path/to/main/project

   # Merge to local staging branch
   git checkout staging
   git merge --no-ff {branch-name} -m "Merge {branch-name}: {task-title}"
   ```

   **Note**: Remote push is NOT performed. User handles deployment manually.

7. **Update ClickUp**:
   - Post completion comment:
     ```bash
     curl -X POST "https://api.clickup.com/api/v2/task/${TASK_ID}/comment" \
       -H "Authorization: ${CLICKUP_TOKEN}" \
       -H "Content-Type: application/json" \
       -d '{"comment_text": "..."}'
     ```
   - Update status to "To Review":
     ```bash
     curl -X PUT "https://api.clickup.com/api/v2/task/${TASK_ID}" \
       -H "Authorization: ${CLICKUP_TOKEN}" \
       -H "Content-Type: application/json" \
       -d '{"status": "To Review"}'
     ```

8. **Kill tmux Session** (if running):
   ```bash
   # Kill any running agent session for this task
   tmux kill-session -t "clickup-${TASK_ID}" 2>/dev/null || true
   ```

9. **Cleanup Worktree** (unless --keep-worktree):
   Use two-phase cleanup to handle orphan directories:
   ```bash
   # Navigate back to main project
   cd /path/to/main/project

   # Phase 1: Git worktree remove
   git worktree remove .worktrees/{branch-name} --force 2>/dev/null || true

   # Phase 2: Remove orphan directory if still exists
   if [ -d ".worktrees/{branch-name}" ]; then
     rm -rf ".worktrees/{branch-name}"
   fi

   # Phase 3: Prune worktree list
   git worktree prune
   ```

   Or use the cleanup script:
   ```bash
   ./scripts/clickup/wt-cleanup.sh .worktrees/{branch-name}
   ```

10. **Show Success**:
    ```
    ✅ Task completed successfully!

    📋 ClickUp: Fix payment webhook race condition
    📦 Version: 0.7.3 → 0.7.4
    🔗 Status: In Progress → To Review
    💬 Comment posted with summary

    🌿 Git:
    ✓ Version bumped and committed
    ✓ Merged to: staging (local)
    🗑️ Worktree removed
    🖥️  tmux session killed

    Task ready for review!
    ```

## Comment Template

```markdown
## Development Complete 🚀

**Branch**: {branch-name}
**Version**: {old-version} → {new-version}
**Merged to**: staging

### Commits
{list of commit messages}

### Files Changed
{list of files with change stats}

### Summary
{brief description based on commits}
```

## Error Handling

- Not on a task branch AND no task_id: "Cannot detect task. Use `/clickup:done <task_id>` or switch to a task branch."
- Invalid branch pattern: "Branch '{branch}' doesn't match task branch pattern (fix|feature|task)/{id}-{slug}"
- Task not found in ClickUp: "Task {task_id} not found in ClickUp. Verify the task ID."
- Uncommitted changes: "You have uncommitted changes. Please commit or stash first."
- Merge conflicts: "Merge conflict detected. Please resolve manually."
- ClickUp update fails: "Git operations complete, but ClickUp update failed. Please update manually."

## Branch Detection Examples

| Current Branch | Detected Task ID |
|----------------|------------------|
| `fix/86bqy2f6j-payment-webhook-race` | `86bqy2f6j` |
| `feature/abc123-user-profile` | `abc123` |
| `task/def456-update-docs` | `def456` |
| `staging` | ❌ Error: Not a task branch |
| `main` | ❌ Error: Not a task branch |

## Pre-merge Checks (Optional)

Before merging, the command can optionally run:
- `npm run check` - Type checking
- `npm run lint` - Linting
- `npm test` - Tests

If any check fails, warn but allow proceeding.

## Related Commands

- `/clickup:process <id>` - Set up worktree and start processing a task
- `/clickup:work <id>` - Spawn agent in tmux session
- `/clickup:attach <id>` - Attach to running agent session
- `/clickup:kill <id>` - Kill agent session without full cleanup
- `/clickup:status` - View all agent sessions

## Tool Coordination

- **Bash**: Git operations and curl for ClickUp API
- **wt-cleanup.sh**: Robust two-phase worktree cleanup script
- **Read**: Load `.env`, check git status
- **AskUser**: Confirm before proceeding with merge and ClickUp updates

ARGUMENTS: $ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/methuz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
