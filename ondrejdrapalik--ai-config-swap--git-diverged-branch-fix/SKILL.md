---
name: git-diverged-branch-fix
description: Resolves git branches that have diverged from their remote counterparts by diagnosing the differences and merging both histories. Use when git status shows "Your branch and 'origin/branch' have diverged" or when local and remote branches have different commits. Use when this capability is needed.
metadata:
  author: ondrejdrapalik
---

# Resolving Diverged Git Branches

When a branch has diverged, it means the local and remote branches have different commits but share a common ancestor. This skill helps diagnose and resolve divergence by preserving both histories.

## Quick Start

```bash
# 1. Diagnose the divergence
git status
git log --oneline --graph --left-right HEAD...origin/<branch-name>

# 2. Merge both histories
git pull --no-rebase origin <branch-name>

# 3. Verify success
git status
```

## Understanding Divergence

A diverged branch looks like this:

```
      Remote: A---B---C---D---E---F---G  (7 commits)
             /
Common: ----*
             \
       Local: 1---2---3---4---5  (5 commits)
```

**Cause:** Both local and remote branches added new commits after their last sync, creating two different histories from a common ancestor.

## Diagnosis Workflow

### Step 1: Check Status

```bash
git status
```

**Look for:**
```
Your branch and 'origin/branch-name' have diverged,
and have X and Y different commits each, respectively.
```

This tells you:
- X = number of local commits not on remote
- Y = number of remote commits not on local

### Step 2: Compare Commits

```bash
git log --oneline --graph --left-right HEAD...origin/<branch-name>
```

**Output format:**
- Lines starting with `<` are local-only commits
- Lines starting with `>` are remote-only commits

Example:
```
> 0bb7f76 Merge branch 'main' into feature
> 2e59237 Add testing docs
< eabfee4 Fix date picker formatting
< 1a08438 Refactor auth context
```

## Resolution Strategies

### Strategy 1: Merge (Recommended for "Keep Both")

**Use when:** You want to preserve both local and remote commits.

```bash
git pull --no-rebase origin <branch-name>
```

**Result:**
- Creates a merge commit combining both histories
- Preserves all commits from both branches
- Shows parallel development actually happened
- No commit history rewriting (safe for shared branches)

**After merge:**
```
      Remote: A---B---C---D---E---F---G
             /                         \
Common: ----*                           M (merge commit)
             \                         /
       Local: 1---2---3---4---5-------/
```

### Strategy 2: Rebase (Linear History)

**Use when:** You want your commits to appear after remote commits.

```bash
git pull --rebase origin <branch-name>
```

**Result:**
- Replays local commits on top of remote commits
- Creates linear history
- **Warning:** Rewrites commit SHAs (changes history)

**After rebase:**
```
Common: ----*---A---B---C---D---E---F---G---1'---2'---3'---4'---5'
```

### Strategy 3: Reset (Discard Local)

**Use when:** Local commits are not important.

```bash
git reset --hard origin/<branch-name>
```

**Result:**
- Discards all local commits
- Branch matches remote exactly
- **Warning:** You lose all local work

## Verification

After resolving, verify the state:

```bash
git status
```

**Expected output after merge:**
```
On branch <branch-name>
Your branch is ahead of 'origin/<branch-name>' by X commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
```

## Common Scenarios

### Scenario 1: User Says "I Want Both"

**Interpretation:** Preserve both local and remote commits.

**Solution:**
```bash
git pull --no-rebase origin <branch-name>
```

### Scenario 2: Merge Conflicts

If merge produces conflicts:

```bash
# 1. Check conflicted files
git status

# 2. Resolve conflicts in each file
# (Edit files manually)

# 3. Mark as resolved
git add <resolved-files>

# 4. Complete merge
git commit
```

### Scenario 3: Already Committed After Divergence

If you committed changes while diverged:

```bash
# Your changes are committed, so merge is safe
git pull --no-rebase origin <branch-name>
```

## Guidelines

### When to Use Each Strategy

**Use Merge when:**
- User wants to keep both histories
- Branch is shared with others
- You want to preserve historical context
- Safety is priority over clean history

**Use Rebase when:**
- You want linear history
- Local commits haven't been pushed
- You're comfortable rewriting history
- Branch is personal/not shared

**Use Reset when:**
- Local commits were mistakes
- You want to start over from remote
- You're absolutely sure local work is disposable

### Important Notes

- `git pull` = `git fetch` + `git merge`
- `--no-rebase` flag ensures merge behavior (ignores git config)
- After resolving, push with `git push origin <branch-name>`
- Never force push (`--force`) to shared branches without team coordination

## Command Reference

| Command | Purpose |
|---------|---------|
| `git status` | Check branch divergence status |
| `git log --oneline --graph --left-right HEAD...origin/<branch>` | Show differing commits |
| `git pull --no-rebase origin <branch>` | Merge both histories |
| `git pull --rebase origin <branch>` | Rebase local onto remote |
| `git reset --hard origin/<branch>` | Discard local, match remote |
| `git push origin <branch>` | Publish merged commits |

## Examples

### Example 1: Merge Strategy

**Input:**
```
User: "I have 5 local commits and 7 remote commits. I want to keep both."
```

**Process:**
```bash
# Diagnose
git log --oneline --graph --left-right HEAD...origin/feature-branch

# Merge
git pull --no-rebase origin feature-branch

# Verify
git status
# Output: Your branch is ahead by 6 commits (5 original + 1 merge)
```

### Example 2: Rebase Strategy

**Input:**
```
User: "Make my branch linear with remote."
```

**Process:**
```bash
git pull --rebase origin feature-branch
# Replays local commits on top of remote
```

### Example 3: Reset Strategy

**Input:**
```
User: "My local commits are wrong, just match remote."
```

**Process:**
```bash
git reset --hard origin/feature-branch
# Discards all local commits
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ondrejdrapalik) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
