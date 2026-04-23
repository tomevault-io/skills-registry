---
name: changelog-generator
description: Transform git commits into customer-friendly release notes and changelogs. Use when preparing releases, generating CHANGELOG.md, or creating release documentation. Use when this capability is needed.
metadata:
  author: allanninal
---

# Changelog Generator

## When to Use This Skill

- Preparing a new release
- Generating CHANGELOG.md updates
- Creating release notes for customers
- Summarizing changes between versions
- Automating release documentation

## Changelog Format (Keep a Changelog)

Follow the [Keep a Changelog](https://keepachangelog.com/) format:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- New feature X for doing Y

### Changed
- Updated dependency Z to version 2.0

### Deprecated
- Feature A will be removed in version 3.0

### Removed
- Dropped support for Node.js 14

### Fixed
- Resolved issue where X caused Y (#123)

### Security
- Updated library X to patch CVE-2024-XXXX

## [1.2.0] - 2024-01-15

### Added
- Dark mode support
- Export to PDF feature

### Fixed
- Memory leak in data processing (#456)

[Unreleased]: https://github.com/user/repo/compare/v1.2.0...HEAD
[1.2.0]: https://github.com/user/repo/compare/v1.1.0...v1.2.0
```

## Commit Convention (Conventional Commits)

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

### Types

| Type | Description | Changelog Section |
|------|-------------|-------------------|
| `feat` | New feature | Added |
| `fix` | Bug fix | Fixed |
| `docs` | Documentation | (usually skip) |
| `style` | Formatting | (usually skip) |
| `refactor` | Code restructure | Changed |
| `perf` | Performance | Changed |
| `test` | Tests | (usually skip) |
| `build` | Build system | (usually skip) |
| `ci` | CI/CD | (usually skip) |
| `chore` | Maintenance | (usually skip) |
| `revert` | Revert commit | Removed |
| `security` | Security fix | Security |
| `deprecate` | Deprecation | Deprecated |

### Breaking Changes

```
feat(api)!: change authentication method

BREAKING CHANGE: API now requires OAuth2 instead of API keys.
Migration guide at https://docs.example.com/migration
```

## Git Commands for Changelog

### Get Commits Since Last Tag

```bash
# List commits since last tag
git log $(git describe --tags --abbrev=0)..HEAD --oneline

# With conventional commit format
git log $(git describe --tags --abbrev=0)..HEAD --pretty=format:"%s"

# Group by type
git log $(git describe --tags --abbrev=0)..HEAD --pretty=format:"%s" | \
  grep -E "^(feat|fix|docs|style|refactor|perf|test|build|ci|chore)(\(.+\))?:" | \
  sort
```

### Get Commits Between Tags

```bash
# Between specific versions
git log v1.0.0..v1.1.0 --oneline

# With author and date
git log v1.0.0..v1.1.0 --pretty=format:"%h - %s (%an, %ar)"
```

### Extract PR Numbers

```bash
# Find PR references in commits
git log v1.0.0..HEAD --oneline | grep -oE "#[0-9]+" | sort -u
```

## Automation Script

```bash
#!/bin/bash
# generate-changelog.sh

VERSION=$1
PREVIOUS_TAG=$(git describe --tags --abbrev=0 2>/dev/null || echo "")

echo "## [$VERSION] - $(date +%Y-%m-%d)"
echo ""

# Added (feat)
ADDED=$(git log ${PREVIOUS_TAG:+$PREVIOUS_TAG..}HEAD --pretty=format:"%s" | grep "^feat" | sed 's/^feat(\([^)]*\)): /- **\1**: /' | sed 's/^feat: /- /')
if [ -n "$ADDED" ]; then
  echo "### Added"
  echo "$ADDED"
  echo ""
fi

# Fixed (fix)
FIXED=$(git log ${PREVIOUS_TAG:+$PREVIOUS_TAG..}HEAD --pretty=format:"%s" | grep "^fix" | sed 's/^fix(\([^)]*\)): /- **\1**: /' | sed 's/^fix: /- /')
if [ -n "$FIXED" ]; then
  echo "### Fixed"
  echo "$FIXED"
  echo ""
fi

# Changed (refactor, perf)
CHANGED=$(git log ${PREVIOUS_TAG:+$PREVIOUS_TAG..}HEAD --pretty=format:"%s" | grep -E "^(refactor|perf)" | sed 's/^\(refactor\|perf\)(\([^)]*\)): /- **\2**: /' | sed 's/^\(refactor\|perf\): /- /')
if [ -n "$CHANGED" ]; then
  echo "### Changed"
  echo "$CHANGED"
  echo ""
fi

# Security
SECURITY=$(git log ${PREVIOUS_TAG:+$PREVIOUS_TAG..}HEAD --pretty=format:"%s" | grep -i "security\|CVE\|vulnerability")
if [ -n "$SECURITY" ]; then
  echo "### Security"
  echo "$SECURITY" | sed 's/^/- /'
  echo ""
fi
```

## Customer-Friendly Release Notes

Transform technical commits into user-facing notes:

### Technical Commit
```
fix(auth): resolve race condition in token refresh causing intermittent 401 errors
```

### Customer-Friendly
```
### Fixed
- Resolved an issue where some users were unexpectedly logged out during long sessions
```

### Transformation Guidelines

| Technical | Customer-Friendly |
|-----------|-------------------|
| "refactor X" | "Improved performance of X" |
| "fix race condition" | "Resolved intermittent issues with..." |
| "add validation" | "Enhanced security of..." |
| "update dependency" | "Improved stability" |
| "fix memory leak" | "Improved application performance" |

## GitHub Release Notes

```bash
# Generate release notes from GitHub
gh release create v1.2.0 --generate-notes

# Create with custom notes
gh release create v1.2.0 --notes-file RELEASE_NOTES.md

# View existing release notes
gh release view v1.2.0
```

## Semantic Versioning

```
MAJOR.MINOR.PATCH

MAJOR: Breaking changes
MINOR: New features (backward compatible)
PATCH: Bug fixes (backward compatible)
```

### When to Bump

| Change Type | Version Bump |
|-------------|--------------|
| Breaking API change | MAJOR |
| New feature | MINOR |
| Bug fix | PATCH |
| Security fix | PATCH (or MINOR if adds feature) |
| Performance improvement | PATCH |
| Deprecation notice | MINOR |

## Checklist

- [ ] Collect all commits since last release
- [ ] Categorize by type (Added, Changed, Fixed, etc.)
- [ ] Rewrite technical details for end users
- [ ] Include issue/PR references
- [ ] Note breaking changes prominently
- [ ] Update version links at bottom
- [ ] Review for sensitive information
- [ ] Proofread for clarity

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
