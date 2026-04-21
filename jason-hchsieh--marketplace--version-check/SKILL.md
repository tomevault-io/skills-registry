---
name: version-check
description: Validate version consistency across all project files, check semver format, git tag alignment, and changelog synchronization per https://semver.org/ Use when this capability is needed.
metadata:
  author: jason-hchsieh
---

# Version Check

You are performing a comprehensive semantic version validation across all project files. This skill ensures compliance with [Semantic Versioning](https://semver.org/) standards.

## Workflow

### 1. Discover Version Files

Search for all known version files using Glob. Check the project root for:

- `package.json`
- `Cargo.toml`
- `pyproject.toml`
- `CMakeLists.txt`
- `.claude-plugin/plugin.json`
- `build.gradle`
- `build.gradle.kts`
- `pom.xml`
- `setup.cfg`
- `version.txt`
- `VERSION`
- `mix.exs`
- `pubspec.yaml`
- `Chart.yaml`

### 2. Extract Versions

For each file found, read it and extract the version string using the appropriate pattern:

| File | Version Pattern |
|------|----------------|
| `package.json` | `"version": "X.Y.Z"` |
| `Cargo.toml` | `version = "X.Y.Z"` (under `[package]`, not dependencies) |
| `pyproject.toml` | `version = "X.Y.Z"` |
| `CMakeLists.txt` | `project(... VERSION X.Y.Z)` |
| `.claude-plugin/plugin.json` | `"version": "X.Y.Z"` |
| `build.gradle` | `version = 'X.Y.Z'` or `version "X.Y.Z"` |
| `build.gradle.kts` | `version = "X.Y.Z"` |
| `pom.xml` | `<version>X.Y.Z</version>` (top-level only) |
| `setup.cfg` | `version = X.Y.Z` |
| `version.txt` | Raw `X.Y.Z` |
| `VERSION` | Raw `X.Y.Z` |
| `mix.exs` | `version: "X.Y.Z"` |
| `pubspec.yaml` | `version: X.Y.Z` |
| `Chart.yaml` | `version: X.Y.Z` |

### 3. Validate Semver Format

For each extracted version, validate it matches semver format:
- Valid: `MAJOR.MINOR.PATCH` (e.g., `1.2.3`)
- Valid: `MAJOR.MINOR.PATCH-prerelease` (e.g., `1.2.3-beta.1`)
- Valid: `MAJOR.MINOR.PATCH+build` (e.g., `1.2.3+build.123`)
- Invalid: anything that doesn't match `^\d+\.\d+\.\d+(-[a-zA-Z0-9.]+)?(\+[a-zA-Z0-9.]+)?$`

### 4. Check Cross-File Consistency

Compare all extracted versions. They should all agree. Flag any mismatches.

### 5. Check Git Tag Alignment

```bash
git tag -l 'v*' --sort=-v:refname | head -1
```

Compare the latest git tag version with the version in project files. Report if they differ (this may be expected if a release is in progress).

### 6. Check CHANGELOG.md Alignment

If CHANGELOG.md exists, extract the top-most version heading (typically `## [X.Y.Z]` or `## X.Y.Z`). Compare with the current project version.

### 7. Report

Generate a comprehensive report:

```
## Version Check Report

### Version Files Found
| File | Version | Status |
|------|---------|--------|
| package.json | 1.2.3 | valid |
| Cargo.toml | 1.2.3 | valid |
| setup.cfg | 1.2 | INVALID (not semver) |

### Consistency Check
- All files agree: YES / NO
- Mismatches: [list any disagreements]

### Git Tag Check
- Latest tag: v1.2.2
- Project version: 1.2.3
- Status: Version ahead of latest tag (expected pre-release)

### CHANGELOG.md Check
- Top version: 1.2.2
- Project version: 1.2.3
- Status: CHANGELOG needs update (run /changelog)

### Issues Found
1. [ISSUE] setup.cfg has invalid semver: "1.2"
2. [WARN] CHANGELOG.md behind project version

### Recommended Actions
1. Fix setup.cfg version to "1.2.0"
2. Run /changelog to update CHANGELOG.md
3. Run /bump to align all files after fixing
```

## Multi-Project Support

If the project is a monorepo (e.g., this marketplace with multiple plugins), scan for version files recursively and group by subdirectory. Report version consistency within each sub-project separately.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jason-hchsieh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
