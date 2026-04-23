---
name: git-sync
description: Synchronize local default branch with remote, ensuring up-to-date base for worktrees. Use when this capability is needed.
metadata:
  author: edanstarfire
---

# Git Sync

## Instructions

### Invocation

Run the sync script. The script path is relative to this skill's directory:

```bash
~/.claude/skills/git-sync/scripts/sync.sh
```

To override the default branch (e.g. if auto-detection fails):

```bash
~/.claude/skills/git-sync/scripts/sync.sh --branch main
```

### Reading the Output

The script emits structured `KEY=VALUE` pairs on stdout. Key fields:

| Field | Meaning |
|---|---|
| `GIT_SYNC_STATUS` | `success` or `error` |
| `DEFAULT_BRANCH` | Detected default branch name |
| `ORIGINAL_BRANCH` | Branch before sync started |
| `COMMITS_PULLED` | Number of new commits pulled (on success) |
| `LATEST_COMMIT` | Latest commit after sync (on success) |
| `ERROR_CODE` | Error type (on failure) |
| `DETAILS` | Error details (on failure) |

### On Success (exit code 0)

Report to the user:

```
Sync complete
- Default branch: <DEFAULT_BRANCH>
- Pulled <COMMITS_PULLED> new commit(s)
- Latest: <LATEST_COMMIT>
- Ready to create new worktrees
```

If `COMMITS_PULLED=0`, simply say the branch is already up to date.

### Error Handling

#### Exit 1 — Uncommitted changes (`ERROR_CODE=uncommitted_changes`)

The default branch has uncommitted changes, which is unexpected. Show the user the `DETAILS` field (modified files) and ask them to choose:

1. **Stash changes** — run `git stash push -m "Temp stash before sync"`, then re-run the sync script
2. **Discard changes** — run `git restore .`, then re-run the sync script
3. **Abort** — stop and let the user handle it manually

#### Exit 2 — Cannot detect default branch (`ERROR_CODE=no_default_branch`)

The script could not determine the default branch automatically. Ask the user which branch to sync (typically `main` or `master`), then re-run with:

```bash
~/.claude/skills/git-sync/scripts/sync.sh --branch <user-specified-branch>
```

#### Exit 3 — Fast-forward failed (`ERROR_CODE=ff_failed`)

The local default branch has diverged from remote. The output includes `LOCAL_COMMITS` and `REMOTE_COMMITS` showing what diverged. Present these to the user and ask them to choose:

1. **Reset to remote** — run `git checkout <DEFAULT_BRANCH> && git reset --hard origin/<DEFAULT_BRANCH>`, warn this discards local commits
2. **Move local commits to a branch** — run `git checkout -b recover/<DEFAULT_BRANCH>-commits && git checkout <DEFAULT_BRANCH> && git reset --hard origin/<DEFAULT_BRANCH>`
3. **Manual resolution** — stop and let the user handle it

After resolving, re-run the sync script to verify.

#### Exit 4 — Fetch failed (`ERROR_CODE=fetch_failed`)

Network or remote issue. Report the error and suggest:
- Check network connectivity
- Verify remote with `git remote -v`
- Retry later

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/edanstarfire) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
