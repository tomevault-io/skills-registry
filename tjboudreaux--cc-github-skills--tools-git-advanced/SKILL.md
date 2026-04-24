---
name: tools-git-advanced
description: Advanced git operations including rebase, cherry-pick, bisect, reflog, stash, and history manipulation for complex workflows. Use when this capability is needed.
metadata:
  author: tjboudreaux
---

# Advanced Git Operations

## Overview
Advanced git commands for complex workflows, history manipulation, and debugging. Use this skill for rebasing, cherry-picking, finding bugs, and recovering from mistakes.

## When to Use
- Rebasing branches and cleaning history
- Cherry-picking specific commits
- Finding which commit introduced a bug
- Recovering lost commits
- Complex merge scenarios
- Stash management

## Interactive Rebase

### Start Rebase
```bash
git rebase -i HEAD~5              # Last 5 commits
git rebase -i main                # Onto main
git rebase -i --root              # All commits
```

### Rebase Commands
| Command | Action |
|---------|--------|
| `pick` | Keep commit as-is |
| `reword` | Change commit message |
| `edit` | Stop to amend commit |
| `squash` | Combine with previous |
| `fixup` | Combine, discard message |
| `drop` | Remove commit |
| `exec` | Run shell command |

### Common Rebase Operations
```bash
# Squash last 3 commits
git rebase -i HEAD~3
# Change 'pick' to 'squash' for commits to combine

# Reorder commits
git rebase -i HEAD~5
# Reorder lines in editor

# Edit commit message
git rebase -i HEAD~3
# Change 'pick' to 'reword'

# Split a commit
git rebase -i HEAD~3
# Change 'pick' to 'edit'
git reset HEAD~
git add -p && git commit
git add -p && git commit
git rebase --continue
```

### Rebase onto Branch
```bash
git rebase main                   # Rebase current onto main
git rebase main feature           # Rebase feature onto main
git rebase --onto main A B        # Rebase B onto main, starting from A
```

### Abort/Continue
```bash
git rebase --continue             # After resolving conflicts
git rebase --skip                 # Skip current commit
git rebase --abort                # Cancel rebase
```

## Cherry-Pick

```bash
git cherry-pick <commit>          # Apply single commit
git cherry-pick A B C             # Multiple commits
git cherry-pick A..B              # Range (exclusive A)
git cherry-pick A^..B             # Range (inclusive A)
git cherry-pick -n <commit>       # No auto-commit
git cherry-pick -x <commit>       # Add source reference
git cherry-pick --abort           # Cancel
git cherry-pick --continue        # After conflict
```

### Cherry-Pick from Another Branch
```bash
git log other-branch --oneline    # Find commit hash
git cherry-pick abc123            # Apply it
```

## Git Bisect (Find Bug)

### Basic Bisect
```bash
git bisect start                  # Start bisecting
git bisect bad                    # Current commit is bad
git bisect good v1.0              # Known good commit
# Git checks out middle commit
# Test and mark:
git bisect good                   # This commit is OK
git bisect bad                    # This commit has bug
# Repeat until found
git bisect reset                  # End bisect
```

### Automated Bisect
```bash
git bisect start HEAD v1.0
git bisect run npm test           # Auto-run test
git bisect run ./test-script.sh   # Custom script
# Script: exit 0 = good, exit 1 = bad
```

### Bisect Log
```bash
git bisect log                    # Show bisect history
git bisect log > bisect.log       # Save for replay
git bisect replay bisect.log      # Replay session
```

## Reflog (Recovery)

### View Reflog
```bash
git reflog                        # HEAD history
git reflog show main              # Branch history
git reflog --date=relative        # With timestamps
```

### Recover Lost Commits
```bash
# After bad reset
git reflog
git reset --hard HEAD@{2}         # Go back 2 moves

# Recover deleted branch
git reflog
git checkout -b recovered HEAD@{5}

# Find lost stash
git fsck --unreachable | grep commit
git show <commit>
```

## Stash Management

### Basic Stash
```bash
git stash                         # Stash changes
git stash -u                      # Include untracked
git stash -a                      # Include ignored
git stash push -m "message"       # With message
git stash push path/to/file       # Specific files
```

### List & Apply
```bash
git stash list                    # List stashes
git stash show                    # Show latest diff
git stash show -p stash@{1}       # Full diff
git stash apply                   # Apply latest
git stash apply stash@{2}         # Apply specific
git stash pop                     # Apply and remove
git stash drop stash@{1}          # Remove stash
git stash clear                   # Remove all
```

### Branch from Stash
```bash
git stash branch newbranch        # Create branch from stash
```

## Reset vs Revert

### Reset (Rewrite History)
```bash
git reset --soft HEAD~1           # Undo commit, keep staged
git reset --mixed HEAD~1          # Undo commit, unstage (default)
git reset --hard HEAD~1           # Undo commit, discard changes
git reset --hard origin/main      # Match remote exactly
```

### Revert (Safe, Creates Commit)
```bash
git revert <commit>               # Revert single commit
git revert HEAD~3..HEAD           # Revert range
git revert -n <commit>            # No auto-commit
git revert -m 1 <merge-commit>    # Revert merge
```

## Amending

```bash
git commit --amend                # Edit last commit message
git commit --amend -m "new msg"   # Replace message
git commit --amend --no-edit      # Add staged, keep message
git commit --amend --author="Name <email>"  # Fix author
```

## Clean & Prune

```bash
git clean -n                      # Dry run
git clean -f                      # Remove untracked files
git clean -fd                     # Include directories
git clean -fX                     # Only ignored files
git gc                            # Garbage collect
git prune                         # Remove unreachable objects
```

## Worktrees

```bash
git worktree list                 # List worktrees
git worktree add ../feature feature-branch
git worktree add ../hotfix -b hotfix
git worktree remove ../feature    # Remove worktree
git worktree prune                # Clean stale
```

## Blame & Log

```bash
git blame <file>                  # Who changed each line
git blame -L 10,20 <file>         # Lines 10-20
git log -p <file>                 # File history with diffs
git log --follow <file>           # Follow renames
git log -S "searchterm"           # Commits adding/removing text
git log -G "regex"                # Commits matching regex
git log --all --oneline --graph   # Visual history
```

## Patches

```bash
git format-patch HEAD~3           # Create patch files
git format-patch main..feature    # Branch patches
git apply patch.patch             # Apply patch
git am patch.patch                # Apply as commit
```

## Troubleshooting

| Scenario | Solution |
|----------|----------|
| Undo last commit | `git reset --soft HEAD~1` |
| Undo pushed commit | `git revert <commit>` |
| Wrong branch commit | Cherry-pick to correct branch |
| Lost commits | `git reflog` to find and recover |
| Bad rebase | `git rebase --abort` or reflog |
| Merge conflict hell | `git merge --abort` and retry |
| Detached HEAD | `git checkout -b newbranch` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tjboudreaux) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
