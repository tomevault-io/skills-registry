---
name: gh-repo
description: Complete Git and GitHub CLI repository management skill. Use when initializing git repos (git init), generating .gitignore files, creating GitHub repositories, cloning, editing, forking, listing, archiving, syncing, or managing repositories. Also handles GitHub CLI authentication (gh auth login, logout, status, token, refresh). Covers the full workflow from local project initialization to GitHub deployment. Includes git init, .gitignore templates for all major languages/frameworks, gh repo create (with all flags), gh repo edit, clone, fork, list, view, delete, archive/unarchive, sync, rename, deploy-key, and set-default commands. Use when this capability is needed.
metadata:
  author: kennethjefferson
---

# Git & GitHub Repository Management

Complete reference for local git initialization and GitHub CLI commands.

## Prerequisites

```bash
git --version    # Verify git is installed
gh auth status   # Check GitHub CLI auth
gh auth login    # If not authenticated
```

## Authentication

See [references/auth.md](references/auth.md) for complete authentication reference.

```bash
# Check authentication status
gh auth status

# Login interactively
gh auth login

# Login with browser
gh auth login --web

# Login to GitHub Enterprise
gh auth login --hostname github.example.com

# Logout
gh auth logout

# Get token for scripts
gh auth token

# Refresh credentials / add scopes
gh auth refresh --scopes "admin:org"

# Setup git credential helper
gh auth setup-git
```

## Quick Reference

| Task | Command |
|------|---------|
| Initialize local repo | `git init` |
| Create .gitignore | See [references/gitignore-templates.md](references/gitignore-templates.md) |
| Create GitHub repo | `gh repo create my-repo --public` |
| Full local→GitHub | See workflow below |
| Clone a repo | `gh repo clone owner/repo` |
| Fork a repo | `gh repo fork owner/repo --clone` |

## Complete Workflow: Local Project to GitHub

Standard workflow to initialize a local project and push to GitHub:

```bash
# 1. Navigate to project directory
cd my-project

# 2. Initialize git repository
git init

# 3. Create .gitignore (generate based on project type)
# See references/gitignore-templates.md for templates

# 4. Stage all files
git add .

# 5. Initial commit
git commit -m "Initial commit"

# 6. Create GitHub repo and push
gh repo create my-project --public --source . --push

# Or step by step:
gh repo create my-project --public
git remote add origin https://github.com/USERNAME/my-project.git
git branch -M main
git push -u origin main
```

## Git Initialization

### git init

Initialize a new git repository in current directory:

```bash
git init                    # Initialize in current directory
git init my-project         # Create directory and initialize
git init --initial-branch=main  # Specify initial branch name
```

### .gitignore Generation

Create appropriate .gitignore based on project type.
See [references/gitignore-templates.md](references/gitignore-templates.md) for complete templates.

**Quick detection:**
- `package.json` → Node.js
- `requirements.txt` / `pyproject.toml` → Python
- `pom.xml` / `build.gradle` → Java
- `go.mod` → Go
- `Cargo.toml` → Rust
- `*.csproj` → .NET
- `Gemfile` → Ruby

## GitHub CLI Commands

### Repository Creation
See [references/repo-create.md](references/repo-create.md) for all flags.

```bash
# Interactive
gh repo create

# Public repo, clone locally
gh repo create my-project --public --clone

# From existing local project
gh repo create --source . --public --push

# With full options
gh repo create my-project --public --description "My project" --gitignore Node --license MIT
```

### Repository Management
See [references/repo-management.md](references/repo-management.md) for all commands.

```bash
# Edit settings
gh repo edit --enable-issues --enable-wiki

# Clone
gh repo clone owner/repo

# Fork and clone
gh repo fork owner/repo --clone

# List repos
gh repo list --limit 50

# View repo
gh repo view --web

# Sync fork
gh repo sync
```

## Common Scenarios

### New Project from Scratch
```bash
mkdir my-app && cd my-app
git init
# Create .gitignore for your stack
echo "# My App" > README.md
git add .
git commit -m "Initial commit"
gh repo create my-app --public --source . --push
```

### Existing Project, No Git
```bash
cd existing-project
git init
# Create appropriate .gitignore
git add .
git commit -m "Initial commit"
gh repo create existing-project --private --source . --push
```

### Clone and Contribute
```bash
gh repo fork owner/project --clone
cd project
git checkout -b my-feature
# Make changes
git add . && git commit -m "Add feature"
git push -u origin my-feature
gh pr create
```

## Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | Error |
| 2 | Command canceled |
| 4 | Authentication required |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kennethjefferson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
