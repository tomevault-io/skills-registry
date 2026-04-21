---
name: git
description: Git commands, workflows, and troubleshooting Use when this capability is needed.
metadata:
  author: bobcob7
---

Help with Git based on `$ARGUMENTS`.

## Common Commands

### Basics
```bash
git status                    # Show working tree status
git add <file>                # Stage file
git add -p                    # Stage interactively (patch mode)
git commit -m "message"       # Commit staged changes
git push                      # Push to remote
git pull                      # Fetch and merge
```

### Branching
```bash
git branch                    # List branches
git branch <name>             # Create branch
git checkout <branch>         # Switch branch
git checkout -b <name>        # Create and switch
git merge <branch>            # Merge branch into current
git rebase <branch>           # Rebase current onto branch
git branch -d <name>          # Delete branch (safe)
git branch -D <name>          # Delete branch (force)
```

### History
```bash
git log --oneline             # Compact log
git log --graph               # With branch graph
git log -p <file>             # Show changes to file
git blame <file>              # Show who changed each line
git show <commit>             # Show commit details
```

### Undoing
```bash
git checkout -- <file>        # Discard working changes
git reset HEAD <file>         # Unstage file
git reset --soft HEAD~1       # Undo commit, keep changes staged
git reset --hard HEAD~1       # Undo commit, discard changes
git revert <commit>           # Create commit that undoes changes
```

### Stashing
```bash
git stash                     # Stash changes
git stash pop                 # Apply and remove stash
git stash list                # List stashes
git stash drop                # Remove stash
```

### Remote
```bash
git remote -v                 # List remotes
git remote add <name> <url>   # Add remote
git fetch <remote>            # Fetch without merge
git push -u origin <branch>   # Push and set upstream
```

## Workflows

### Feature Branch
1. `git checkout -b feature/name`
2. Make changes, commit
3. `git push -u origin feature/name`
4. Create PR
5. Merge and delete branch

### Rebase Workflow
1. `git fetch origin`
2. `git rebase origin/main`
3. Resolve conflicts if any
4. `git push --force-with-lease`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bobcob7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
