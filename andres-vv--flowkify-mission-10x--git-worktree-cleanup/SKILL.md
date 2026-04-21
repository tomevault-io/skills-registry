---
name: git-worktree-cleanup
description: Clean up git worktrees whose branches have been merged or whose PRs are closed. Use when the user asks to clean up worktrees, remove old worktrees, prune stale branches, or tidy up after merged PRs. Use when this capability is needed.
metadata:
  author: andres-vv
---

<!-- ================================================================
     PROJECT CONFIGURATION — update these values for your project
     ================================================================ -->

## Configuration

| Variable | Value |
|----------|-------|
| `GITHUB_ORG` | |
| `ADO_PROJECT` | |
| `BASE_BRANCH` | |

<!-- ================================================================ -->

# Git Worktree Cleanup

Remove worktrees whose feature branches have been merged into `{BASE_BRANCH}` or whose PRs are closed/merged.

## Workflow

### Step 1: Run the cleanup script

```bash
bash scripts/worktree/cleanup-worktrees.sh
```

The script handles **git-merged** branches (where git sees the branch tip as ancestor of `origin/{BASE_BRANCH}`). It will:

1. Fetch latest from remote
2. Remove worktrees whose branch is merged into `origin/{BASE_BRANCH}`
3. Delete merged local branches that have no worktree
4. Prune stale worktree references
5. **List remaining worktrees** that the script couldn't determine as merged

### Step 2: Check remaining worktrees via GitHub MCP (squash-merged PRs)

`git branch --merged` does NOT detect squash-merged branches because the branch tip is not an ancestor of `{BASE_BRANCH}`. Use **GitHub MCP** to check PR status for each remaining worktree branch.

For each remaining worktree branch listed by the script:

1. Use **user-github** MCP `list_pull_requests` with:
   - `owner`: repo owner (e.g. `{GITHUB_ORG}`)
   - `repo`: repo name
   - `state`: `closed`
   - `head`: `<owner>:<branch-name>` (e.g. `{GITHUB_ORG}:bugfix/short-description`)
   - `base`: `{BASE_BRANCH}`
2. If the PR `merged_at` is set (i.e. it was merged, not just closed), remove the worktree:
   ```bash
   git worktree remove <worktree-path>
   git branch -D <branch-name>
   git worktree prune
   ```

### Step 3: Resolve ADO work items for merged branches

For each branch that was removed (by the script or by the agent in step 2), if the branch name contains a ticket number (e.g. `AB-1134` in `feature/AB-1134-short-description`):

1. Extract the numeric work item ID (e.g. `1134`)
2. Use **user-ado** MCP `wit_update_work_item` with:
   - `id`: the work item ID (integer)
   - `updates`: `[{ "op": "add", "path": "/fields/System.State", "value": "Resolved" }]`
3. Use project **{ADO_PROJECT}** (per ado-project-scope skill)

## Targeted cleanup (single worktree)

To remove a specific worktree manually:

```bash
git worktree remove ../worktrees/feature-AB-<ticket>-<short-desc>
git branch -d feature/AB-<ticket>-<short-desc>
```

## Important notes

- The script only removes worktrees under `../worktrees/` — it never touches the main working directory.
- Only branches matching `feature/*`, `bugfix/*`, `qol/*`, or `fix/*` are considered for cleanup.
- If a branch has uncommitted changes, `git worktree remove` will fail safely — no data loss.
- Use `git branch -D` (force) for squash-merged branches since git won't recognize them as merged.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andres-vv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
