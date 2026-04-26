---
name: semantic-release
description: Automate versioning and release notes from conventional commits Use when this capability is needed.
metadata:
  author: erp-core-dev
---

# Semantic Release

Automate version bumps and changelog generation from conventional commits.

## What To Do

1. **For Node.js projects**:
   ```bash
   npm install --save-dev semantic-release @semantic-release/changelog @semantic-release/git
   ```

2. **For .NET** (GitVersion):
   ```bash
   dotnet tool install -g GitVersion.Tool
   dotnet-gitversion /showvariable SemVer
   ```

3. **CI Integration**:
   ```yaml
   - script: npx semantic-release --dry-run
     displayName: "Preview Release"
   ```

## Arguments
- `--dry-run`: Preview without publishing
- `--branch=<name>`: Release branch (default: main)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erp-core-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
