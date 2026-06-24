---
name: release-process-skill
description: ⚠️ **IMPORTANT**: PATs expire frequently and may only work for a single publish session. Use when this capability is needed.
metadata:
  author: fabioc-aloha
---

# Release Process Skill

**Classification**: Master-Only Skill | Process Automation
**Activation**: release, publish, marketplace, vsix, version bump, pre-release
**Inheritance**: master-only (contains PAT handling, marketplace credentials)

---

## Purpose

Comprehensive knowledge for releasing Alex Cognitive Architecture to VS Code Marketplace and managing version lifecycle.

---

## Quick Reference

### Release Commands

```powershell
# From repo root
.\scripts\release-vscode.ps1 -BumpType patch              # Stable release
.\scripts\release-vscode.ps1 -BumpType minor -PreRelease  # Pre-release
.\scripts\release-vscode.ps1 -BumpType patch -DryRun      # Test without publishing
```

### Manual Publishing

```powershell
cd platforms/vscode-extension
npx vsce publish --pre-release  # Pre-release
npx vsce publish                # Stable release
```

---

## PAT (Personal Access Token) Setup

> ⚠️ **IMPORTANT**: PATs expire frequently and may only work for a single publish session.
> Always create a fresh PAT before each release to avoid 401 errors.

### Creating a New PAT

1. **Via Marketplace** (Recommended):
   - Go to: https://marketplace.visualstudio.com/manage/publishers/
   - Click your publisher name
   - Click "..." menu → "Generate new token"
   - Copy the token immediately (shown only once)

2. **Via Azure DevOps**:
   - Go to: https://dev.azure.com/
   - Click User Settings (gear icon) → Personal Access Tokens
   - Click "New Token"
   - Name: `vsce-marketplace` (or similar)
   - Organization: `All accessible organizations`
   - Expiration: Set appropriate duration (max 1 year)
   - Scopes: Select `Marketplace` → `Manage`
   - Click Create, copy token

### Storing the PAT

**Option 1: Environment Variable** (Session only)
```powershell
$env:VSCE_PAT = "your-token-here"
```

**Option 2: .env File** (Persistent, gitignored)
```
# platforms/vscode-extension/.env
VSCE_PAT=your-token-here
```

**Option 3: System Environment** (Persistent)
```powershell
[Environment]::SetEnvironmentVariable("VSCE_PAT", "your-token", "User")
```

### PAT Troubleshooting

| Error | Cause | Solution |
|-------|-------|----------|
| 401 Unauthorized | PAT expired or invalid | Create new PAT |
| 401 Unauthorized | Wrong scope | Ensure "Marketplace (Manage)" scope |
| 403 Forbidden | Not publisher owner | Check publisher membership |
| Token not found | .env not loaded | Check file path, run preflight |

---

## Version Strategy

### Semantic Versioning

```
MAJOR.MINOR.PATCH
  │     │     └── Bug fixes, docs
  │     └──────── New features, non-breaking
  └────────────── Breaking changes
```

### Pre-Release vs Stable

| Type | Flag | Visibility | Use Case |
|------|------|------------|----------|
| Pre-release | `--pre-release` | Opt-in only | Beta testing |
| Stable | (none) | Everyone | Production ready |

**VS Code Marketplace Rule**: Pre-release versions must use the `--pre-release` flag, NOT semver suffixes like `-beta.1`.

### Version Files to Update

When bumping version, these files need synchronization:

1. `platforms/vscode-extension/package.json` → `version` field
2. `platforms/vscode-extension/.github/copilot-instructions.md` → `**Version**:` line
3. `CHANGELOG.md` → New `## [X.Y.Z]` section

The `release-vscode.ps1` script handles all of these automatically.

---

## Release Workflow

### Automated (Recommended)

```
┌─────────────────────────────────────────────────────────────────┐
│  .\scripts\release-vscode.ps1 -BumpType patch -PreRelease       │
└─────────────────────────────────────────────────────────────────┘
         │
         ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│ 1. Preflight    │───▶│ 2. Version Bump │───▶│ 3. CHANGELOG    │
│ - PAT check     │    │ - package.json  │    │ - Add entry     │
│ - Build check   │    │ - heir version  │    │ - Date stamp    │
│ - Lint check    │    │                 │    │                 │
│ - Version sync  │    │                 │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │
         ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│ 4. Human Review │───▶│ 5. Git Commit   │───▶│ 6. Publish      │
│ - Confirm (y/N) │    │ - Commit        │    │ - vsce publish  │
│ - Edit CHANGELOG│    │ - Tag           │    │ - --pre-release │
│                 │    │ - Push          │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### Manual Checklist

If not using the script:

- [ ] Run preflight: `.\scripts\release-preflight.ps1 -Package`
- [ ] Bump version in `package.json`
- [ ] Update heir `copilot-instructions.md` version
- [ ] Add CHANGELOG entry
- [ ] Commit: `git commit -m "chore: release vX.Y.Z"`
- [ ] Tag: `git tag vX.Y.Z`
- [ ] Push: `git push && git push --tags`
- [ ] Publish: `npx vsce publish [--pre-release]`

---

## Preflight Checks

The `release-preflight.ps1` script validates:

| Check | What It Does |
|-------|--------------|
| PAT | Verifies VSCE_PAT is available |
| Version Sync | package.json = CHANGELOG = heir instructions |
| Build | `npm run compile` succeeds |
| Lint | `npm run lint` passes |
| Tests | `npm test` passes (can skip with `-SkipTests`) |
| Git Status | Shows uncommitted changes |
| Git Tags | Warns if tag already exists |
| Package | Creates VSIX (with `-Package` flag) |

---

## File Structure

```
Alex_Plug_In/
├── scripts/
│   ├── release-preflight.ps1    # Pre-release validation
│   ├── release-vscode.ps1       # Full release automation
│   └── build-extension-package.ps1  # Heir sync
├── platforms/vscode-extension/
│   ├── package.json             # Version source of truth
│   ├── .env                     # PAT storage (gitignored)
│   ├── .github/
│   │   └── copilot-instructions.md  # Heir version
│   └── *.vsix                   # Built packages
└── CHANGELOG.md                 # Version history
```

---

## Common Issues

### "The pre-release version is not valid"

**Cause**: Used semver suffix like `3.7.4-beta.1`

**Solution**: Use plain version `3.7.4` with `--pre-release` flag

### "401 Unauthorized"

**Cause**: PAT expired, invalid, or wrong scope

**Solution**:
1. Create new PAT at marketplace.visualstudio.com/manage/publishers
2. Ensure "Marketplace (Manage)" scope
3. Update .env or environment variable

### "Version already exists"

**Cause**: Trying to publish same version twice

**Solution**: Bump version first, or delete existing version from marketplace

### Build succeeds but publish fails

**Cause**: Often network or auth issues

**Solution**:
1. Check internet connection
2. Verify PAT is valid
3. Try `npx vsce login fabioc-aloha` first

---

## Links

- [VS Code Publishing Guide](https://code.visualstudio.com/api/working-with-extensions/publishing-extension)
- [Marketplace Publisher Portal](https://marketplace.visualstudio.com/manage/publishers/)
- [Azure DevOps PAT Docs](https://learn.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)
- [VSCE CLI Reference](https://github.com/microsoft/vscode-vsce)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fabioc-aloha) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
