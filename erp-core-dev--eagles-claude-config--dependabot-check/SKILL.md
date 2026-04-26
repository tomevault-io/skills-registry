---
name: dependabot-check
description: Check and update vulnerable dependencies Use when this capability is needed.
metadata:
  author: erp-core-dev
---

# Dependency Vulnerability Check

Check NuGet and npm dependencies for known vulnerabilities.

## What To Do

1. **.NET dependencies**:
   ```bash
   dotnet list package --vulnerable --include-transitive
   dotnet list package --outdated
   ```

2. **npm dependencies**:
   ```bash
   npm audit --json > npm-audit.json
   npm audit fix
   ```

3. **Configure Dependabot** (.github/dependabot.yml):
   ```yaml
   version: 2
   updates:
     - package-ecosystem: "nuget"
       directory: "/src/backend"
       schedule: { interval: "weekly" }
     - package-ecosystem: "npm"
       directory: "/src/frontend"
       schedule: { interval: "weekly" }
   ```

4. **Update specific package**: `dotnet add package PackageName --version X.Y.Z`

## Arguments
- `--ecosystem=<type>`: nuget or npm
- `--auto-merge`: Auto-merge minor/patch updates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erp-core-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
