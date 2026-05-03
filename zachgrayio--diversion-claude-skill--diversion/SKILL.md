---
name: diversion
description: Diversion (dv) cloud-native version control. When Claude needs to help with source control operations using Diversion including: initializing repositories, cloning, committing changes, branching, merging, viewing diffs and logs, or any other version control tasks. This skill covers all dv CLI commands. Use when this capability is needed.
metadata:
  author: zachgrayio
---

# Diversion Version Control

## Overview

Diversion (dv) is a cloud-native version control system designed for modern development workflows. Unlike traditional VCS, Diversion provides real-time sync, simplified branching, and a web dashboard for collaboration.

**Key Concepts:**
- **Repository**: A cloud-hosted project containing all files and history
- **Workspace**: A local working copy synced with the cloud repository
- **Branch**: An isolated line of development
- **Commit**: A snapshot of changes with a message

## Workflow Decision Tree

### Starting a New Project
Use `dv init` to create a new repository from local files

### Working on an Existing Project
Use `dv clone` to get a working copy of a repository

### Making Changes
1. Edit files locally (changes sync automatically)
2. Check status with `dv status`
3. Commit with `dv commit -m "message" -a`

### Branching Workflow
1. Create branch: `dv branch -c feature-name`
2. Work and commit on feature branch
3. Preview merge: `dv merge-preview main`
4. Merge: `dv merge feature-name --into main`

### Updating Your Workspace
Use `dv update` to pull latest changes from the checked-out branch

## Command Reference

### Repository Commands
- [init](commands/init.md): Initialize a new repository
- [clone](commands/clone.md): Clone a repository to local drive
- [status](commands/status.md): Show working tree and sync status
- [repo](commands/repo.md): List or delete repositories

### Workspace Commands
- [workspace](commands/workspace.md): List, pause, resume, rename, or delete workspaces
- [checkout](commands/checkout.md): Switch branches, commits, or tags
- [preferences](commands/preferences.md): Configure folder sync preferences
- [view](commands/view.md): Open workspace in web browser

### Commit and Branch Commands
- [commit](commands/commit.md): Create a commit
- [branch](commands/branch.md): List, create, delete, or rename branches
- [branch-name](commands/branch-name.md): Show current branch name
- [log](commands/log.md): Show commit history
- [diff](commands/diff.md): Show differences between commits or workspace
- [merge](commands/merge.md): Merge branches or commits
- [merge-preview](commands/merge-preview.md): Preview merge changes
- [revert](commands/revert.md): Revert changes from a commit
- [revert-to-commit](commands/revert-to-commit.md): Revert workspace to a specific commit
- [cherry-pick](commands/cherry-pick.md): Apply a specific commit
- [restore](commands/restore.md): Restore file from commit, branch, or tag
- [reset](commands/reset.md): Reset workspace changes
- [clean](commands/clean.md): Remove untracked files
- [tag](commands/tag.md): List, create, modify, or delete tags

### Collaboration Commands
- [share](commands/share.md): Share workspace with users
- [invite](commands/invite.md): Invite collaborators to repository
- [update](commands/update.md): Pull latest changes from branch

### Navigation
- [cd](commands/cd.md): Change working directory

### Advanced Commands
- [import](commands/import.md): Import a git repository
- [support](commands/support.md): Create and upload support bundle
- [unregister](commands/unregister.md): Unregister local directory from sync

### Utility Commands
- [help](commands/help.md): Print command usage
- [login](commands/login.md): Log into Diversion account
- [authenticate](commands/authenticate.md): Authenticate with OAuth token
- [logout](commands/logout.md): Log out of account
- [feedback](commands/feedback.md): Send feedback to developers

## Common Workflows

### Initialize and First Commit
```bash
dv init . my-project
# edit files
dv commit -m "Initial commit" -a
```

### Clone and Start Working
```bash
dv clone my-project ./my-project
cd my-project
# edit files - changes sync automatically
dv status
dv commit -m "My changes" -a
```

### Feature Branch Workflow
```bash
dv branch -c feature/new-feature
# make changes and commit
dv commit -m "Add new feature" -a
dv checkout main
dv merge feature/new-feature
```

### View Changes Before Committing
```bash
dv status          # see changed files
dv diff            # see actual changes
dv diff path/file  # diff specific file
```

## Reference Links

- [Diversion Documentation](https://docs.diversion.dev/)
- [Command Reference](https://docs.diversion.dev/cmd-ref/)
- [Discord Community](https://discord.gg/9UtVyDkPS2)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zachgrayio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
