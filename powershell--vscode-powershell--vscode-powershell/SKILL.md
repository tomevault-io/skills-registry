---
name: update-npm-packages
description: > Use when this capability is needed.
metadata:
  author: PowerShell
---

# Updating NPM Packages

Read [docs/development.md](../../../docs/development.md) "Tracking Upstream Dependencies"
section for full context.

## Rules

- Dependencies are split into three groups:
    - `dependencies` — runtime packages bundled into the extension
    - `devDependencies` — build tools only (minimum for Azure DevOps pipeline)
    - `optionalDependencies` — lint, type-checking, and test tools (for development and GitHub Actions)
- Always use `npm install --include=optional` to get all three groups.
- The `.npmrc` uses an Azure Artifacts mirror; read its comments for authentication instructions.
- When updating `engines.vscode` follow the "Tracking Upstream Dependencies" section of `docs/development.md`.
- Update the ESLint packages (`eslint`, `@eslint/js`, `typescript-eslint`,
  `eslint-config-prettier`) together and fix any new lint warnings.
- After updating, verify: `npm run compile`, `npm run lint`, `npm audit`.
- For vulnerabilities in transitive dependencies identified by `npm audit`, add an `overrides` entry in `package.json`
  rather than using `npm audit fix --force` which may downgrade our packages.
- Check that each `overrides` entry is still necessary.

---
> Source: [PowerShell/vscode-powershell](https://github.com/PowerShell/vscode-powershell) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-27 -->
