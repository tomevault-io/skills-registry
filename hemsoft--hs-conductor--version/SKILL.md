---
name: version
description: V1.0 - Release version management checklist for hs-cli-conductor. Use when bumping versions or preparing releases. Use when this capability is needed.
metadata:
  author: hemsoft
---

# Version Management

Release version checklist for hs-cli-conductor using **Semantic Versioning** (major.minor.patch).

## Version Bump Checklist

When releasing a new version, update ALL of these locations:

### 1. Changelog

- [ ] **`CHANGELOG.md`** (root) - Add entry under `[Unreleased]` or new version section
- Format: Keep a Changelog standard (<https://keepachangelog.com>)
- Include: Added, Changed, Fixed, Removed sections as needed

### 2. Package Files

- [ ] **`package.json`** (root) - `"version": "x.y.z"`
- [ ] **`admin/package.json`** - `"version": "x.y.z"` (keep in sync)

### 3. UI Version Displays

- [ ] **`admin/src/components/TitleBar.tsx`**
  - Line ~69: Alert message `Version x.y.z`
  - Line ~139: Title bar display `Vx.y`

### 4. Build Configuration

- [ ] **`admin/electron-builder.json5`** - Uses `${version}` from admin/package.json (auto-resolved)
- [ ] Verify output directory: `release/${version}/`

## Quick Command

To bump version across all files:

```powershell
# Replace OLD_VERSION with current, NEW_VERSION with target
$old = "0.1.0"; $new = "0.2.0"

# Update package.json files
(Get-Content package.json) -replace "`"version`": `"$old`"", "`"version`": `"$new`"" | Set-Content package.json
(Get-Content admin/package.json) -replace "`"version`": `"$old`"", "`"version`": `"$new`"" | Set-Content admin/package.json

# TitleBar requires manual review for display format
```

## Current State

**Note:** Version inconsistency detected during skill creation:

- Root `package.json`: 0.1.0
- Admin `package.json`: 0.0.0
- TitleBar alert: 0.1.0
- TitleBar display: V1.0

**Recommendation:** Align all versions before next release.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hemsoft) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
