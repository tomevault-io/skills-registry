---
name: changelog-writer
description: Expert changelog writing including version history, release notes, and migration guides Use when this capability is needed.
metadata:
  author: ljchg12-hue
---

# Changelog Writer

## Purpose
Create and maintain changelogs, release notes, and version history following best practices and conventional formats.

## Activation Keywords
- changelog, change log
- release notes, version history
- what's new, version changes
- migration guide, breaking changes
- keep a changelog

## Core Capabilities

### 1. Changelog Generation
- Keep a Changelog format
- Conventional Commits parsing
- Semantic versioning
- Automated extraction
- Category organization

### 2. Release Notes
- Feature highlights
- Breaking changes
- Bug fixes
- Performance improvements
- Deprecation notices

### 3. Migration Guides
- Version upgrade steps
- Breaking change handling
- Code migration examples
- Compatibility notes
- Rollback procedures

### 4. Version Management
- Semantic versioning
- Pre-release versions
- Version comparison
- Release scheduling
- Hotfix documentation

### 5. Format Standards
- Keep a Changelog
- Conventional Commits
- GitHub Releases
- npm changelogs
- Custom formats

## Keep a Changelog Format

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/),
and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added
- New feature description

### Changed
- Changed feature description

### Deprecated
- Deprecated feature description

### Removed
- Removed feature description

### Fixed
- Bug fix description

### Security
- Security fix description

## [1.0.0] - 2024-01-15

### Added
- Initial release features

[Unreleased]: https://github.com/user/repo/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/user/repo/releases/tag/v1.0.0
```

## Change Categories

| Category | Description | Example |
|----------|-------------|--------|
| Added | New features | New API endpoint |
| Changed | Existing functionality changes | Updated UI design |
| Deprecated | Soon-to-be removed | Old API version |
| Removed | Removed features | Legacy support |
| Fixed | Bug fixes | Crash on startup |
| Security | Security patches | XSS vulnerability |

## Release Notes Template

```markdown
# Release v1.2.0

**Release Date:** January 15, 2024

## Highlights

🎉 **Major Feature** - Description of the main highlight

## What's New

### New Features
- **Feature Name** - Brief description ([#123](link))
- **Another Feature** - Brief description ([#124](link))

### Improvements
- Improved X performance by 50%
- Better error messages for Y

### Bug Fixes
- Fixed issue where X would fail ([#125](link))
- Resolved crash when doing Y ([#126](link))

## Breaking Changes

⚠️ **API Change**: `oldMethod()` has been renamed to `newMethod()`

**Before:**
```javascript
client.oldMethod();
```

**After:**
```javascript
client.newMethod();
```

## Deprecations

- `deprecatedFunction()` is deprecated and will be removed in v2.0
- Use `newFunction()` instead

## Upgrade Guide

1. Update your package: `npm update package-name`
2. Rename `oldMethod` calls to `newMethod`
3. Review deprecation warnings

## Full Changelog

[v1.1.0...v1.2.0](https://github.com/user/repo/compare/v1.1.0...v1.2.0)
```

## Example Usage

```
User: "Write changelog entry for version 2.1.0"

Changelog Writer Response:

## [2.1.0] - 2024-01-20

### Added
- **Dark Mode Support** - Added system-wide dark mode with automatic
  detection and manual toggle ([#234](link))
- **Export to PDF** - Users can now export reports to PDF format
  ([#238](link))
- New `--verbose` flag for CLI to show detailed output ([#241](link))

### Changed
- Improved dashboard loading performance by 40% through lazy loading
- Updated dependencies to latest versions
- Enhanced error messages with actionable suggestions

### Fixed
- Fixed memory leak when processing large files ([#235](link))
- Resolved issue where settings weren't persisted after restart
  ([#237](link))
- Fixed incorrect timezone handling in scheduled tasks ([#239](link))

### Security
- Updated `axios` to 1.6.0 to address CVE-2023-XXXXX

### Deprecated
- `legacyExport()` function is deprecated; use `exportTo()` instead
  (will be removed in v3.0)

---

**Full Changelog**: [v2.0.0...v2.1.0](https://github.com/user/repo/compare/v2.0.0...v2.1.0)

**Docker Image**: `ghcr.io/user/repo:2.1.0`

**npm**: `npm install package@2.1.0`
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ljchg12-hue) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
