---
name: release-management
description: Automated versioning, changelog generation, and git tagging using release.sh script with semantic versioning and conventional commits Use when this capability is needed.
metadata:
  author: hackur
---

# Release Management

Automated release workflow using `./scripts/release.sh` for semantic versioning, changelog generation, and git tagging.

## When to Use

- Creating new releases (patch, minor, major)
- Bumping version numbers across package.json and composer.json
- Generating changelogs from git commits
- Creating git tags for releases
- Preparing for production deployments

## Quick Commands

```bash
# Check current version
./scripts/release.sh current

# Preview patch release (dry-run default)
./scripts/release.sh prepare patch

# Execute full release workflow
DRY_RUN=false ./scripts/release.sh full patch

# Individual steps
./scripts/release.sh changelog        # Generate changelog only
./scripts/release.sh tag             # Create git tag
./scripts/release.sh publish         # Push to remote
```

## Release Workflow

### 4-Step Process

1. **Current** - Show version info and status
2. **Prepare** - Bump versions + generate changelog
3. **Tag** - Create git tag from current version
4. **Publish** - Push tags and commits to remote

### Full Workflow (One Command)

```bash
DRY_RUN=false ./scripts/release.sh full patch
```

This executes: prepare → tag → publish in sequence.

## Semantic Versioning

| Type | Version Change | Example | Use Case |
|------|---------------|---------|----------|
| **patch** | 1.0.0 → 1.0.1 | Bug fixes, security patches | Most common |
| **minor** | 1.0.0 → 1.1.0 | New features (backward compatible) | New functionality |
| **major** | 1.0.0 → 2.0.0 | Breaking changes | API changes |

## Conventional Commits

Changelog generator recognizes these commit types:

```bash
# Features (→ Features section in changelog)
git commit -m "feat: add user authentication system"

# Bug fixes (→ Bug Fixes section)
git commit -m "fix: correct validation error in login form"

# Other types (→ Respective sections)
git commit -m "chore: update dependencies"
git commit -m "docs: add API documentation"
git commit -m "refactor: simplify payment processing"
git commit -m "test: add unit tests for promo codes"
git commit -m "style: format code with prettier"
git commit -m "perf: optimize database queries"
git commit -m "ci: update GitLab pipeline config"
git commit -m "build: configure webpack settings"
```

## Files Updated

The release script updates 3 files:

1. **package.json** - `version` field
2. **composer.json** - `version` field
3. **CHANGELOG.md** - Prepends new release entry

### Changelog Format

```markdown
## [1.2.3] - 2025-10-28

### Features
- Add user authentication system
- Implement password reset flow

### Bug Fixes
- Correct validation error in login form
- Fix memory leak in image processing

### Chores
- Update Laravel to 12.0
- Upgrade Node dependencies
```

## Safety Features

### Dry-Run by Default

All commands run in preview mode unless explicitly disabled:

```bash
# Safe preview (default)
./scripts/release.sh prepare patch

# Actual execution
DRY_RUN=false ./scripts/release.sh prepare patch
```

### Pre-Flight Validation

Script checks:
- ✅ Working directory is clean (no uncommitted changes)
- ✅ On correct branch (main/master)
- ✅ All dependencies installed (node, composer)
- ✅ No existing tag conflicts

### Interactive Confirmations

Prompts before:
- Bumping version numbers
- Creating git tags
- Pushing to remote repository

### Rollback Support

If release fails:

```bash
# Manually revert version changes
git checkout -- package.json composer.json CHANGELOG.md

# Delete local tag
git tag -d v1.2.3

# Delete remote tag (if published)
git push origin :refs/tags/v1.2.3
```

## Integration with Deployment

### Production Deployment Flow

```bash
# 1. Create release
DRY_RUN=false ./scripts/release.sh full patch

# 2. Deploy to production (uses latest tag)
./scripts/prod.sh deploy
```

### CI/CD Integration

**GitLab CI/CD**:
- **Staging**: Auto-deploy on push to main
- **Production**: Manual deploy on tag creation

When you create a release tag, GitLab pipeline triggers production deployment job.

## Common Pitfalls

### ❌ WRONG: Forgetting DRY_RUN=false

```bash
# This only previews changes, doesn't execute
./scripts/release.sh full patch
```

**✅ CORRECT**: Explicitly disable dry-run

```bash
DRY_RUN=false ./scripts/release.sh full patch
```

### ❌ WRONG: Dirty working directory

```bash
# Uncommitted changes will block release
git status
# modified: app/Models/User.php
./scripts/release.sh prepare patch
# ERROR: Working directory not clean
```

**✅ CORRECT**: Commit or stash changes first

```bash
git add .
git commit -m "feat: update user model"
./scripts/release.sh prepare patch
```

### ❌ WRONG: Wrong branch

```bash
# On feature branch
git branch
# * feature/new-auth
./scripts/release.sh prepare patch
# ERROR: Not on main branch
```

**✅ CORRECT**: Switch to main branch

```bash
git checkout main
git pull origin main
./scripts/release.sh prepare patch
```

## Example Workflow

### Complete Release Process

```bash
# 1. Ensure clean state
git status
git checkout main
git pull origin main

# 2. Check current version
./scripts/release.sh current
# Current version: 1.2.0

# 3. Preview patch release
./scripts/release.sh prepare patch
# Would bump: 1.2.0 → 1.2.1
# Preview changelog...

# 4. Execute release
DRY_RUN=false ./scripts/release.sh full patch
# ✅ Version bumped to 1.2.1
# ✅ Changelog generated
# ✅ Tag created: v1.2.1
# ✅ Pushed to origin

# 5. Deploy to production
./scripts/prod.sh deploy
```

## Documentation Links

- **Complete Guide**: `docs/deployment/RELEASE-MANAGEMENT.md`
- **Package Versioning**: `docs/development/PACKAGE-VERSIONING-STRATEGY.md`
- **Deployment Guide**: `docs/deployment/DEPLOYMENT-GUIDE.md`
- **Conventional Commits**: https://www.conventionalcommits.org/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hackur) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
