---
name: git-commands
description: Execute Git version control operations Use when this capability is needed.
metadata:
  author: sdkwork-cloud
---

# Git Commands

You can perform Git version control operations.

When to use this skill:
- User wants to commit changes
- User needs to create or switch branches
- User requests to push or pull changes
- User wants to view git status or history
- User needs to merge or rebase branches
- User wants to clone or initialize repositories

How to use this skill:
1. Verify we're in a Git repository
2. Understand the Git operation requested
3. Execute the appropriate Git command
4. Report the result or any errors

Available operations:
- **status**: Show working tree status
- **init**: Initialize a new repository
- **clone**: Clone a repository
- **add**: Add files to staging area
- **commit**: Commit staged changes
- **push**: Push commits to remote
- **pull**: Pull changes from remote
- **branch**: List or create branches
- **checkout**: Switch branches or restore files
- **merge**: Merge branches
- **rebase**: Rebase current branch
- **log**: Show commit history
- **diff**: Show changes between commits
- **stash**: Stash local changes

Best practices:
- Always check git status before operations
- Pull before pushing to avoid conflicts
- Write clear, descriptive commit messages
- Use meaningful branch names
- Review changes before committing
- Handle merge conflicts carefully
- Backup work before destructive operations

Parameters:
- operation: Git command to execute (required)
- repository: Repository path or URL (for clone/init)
- files: File patterns (for add/diff)
- message: Commit message (for commit)
- branch: Branch name (for branch/checkout/merge)
- remote: Remote name or URL (for push/pull)
- options: Additional git flags

Examples:
User: "Check git status"
→ Execute git status and show the results

User: "Create a new branch called 'feature/new-api'"
→ Execute git branch feature/new-api

User: "Commit all changes with message 'Add user authentication'"
→ Execute git add . && git commit -m "Add user authentication"

User: "Push current branch to origin"
→ Execute git push origin <current-branch>

User: "Show last 5 commits"
→ Execute git log -5

User: "Merge branch 'develop' into current branch"
→ Execute git merge develop

Limitations:
- Requires Git to be installed
- Some operations require network access (push/pull/clone)
- May fail due to merge conflicts
- Requires proper permissions for repository access
- Some operations may fail if repository is locked or busy

Safety warnings:
- **DESTRUCTIVE OPERATIONS**: Some git operations can be hard to reverse
- Always check git status before destructive operations
- Use --dry-run flag when available to preview changes
- Backup important work before rebase or reset
- Force push can overwrite remote history - use with caution

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sdkwork-cloud) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
