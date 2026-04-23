---
name: import-path-updater
description: Rewrite imports and paths in the NEW microservice after copying a feature from an existing service. Use on prompts like "fix imports in new microservice", "update paths after extraction", or any post-copy refactor. Handles Webpack aliases, relative paths, Jest mock paths, and Playwright test references. Always runs a dry-run first before applying changes. The original microservice is NOT modified. Use when this capability is needed.
metadata:
  author: teragroh
---

## Instructions

### Key Principle

Only the **new microservice** gets import rewrites. The original stays untouched. The goal is to make the copied feature compile and run in the new service's directory structure.

### Inputs

1. **Original path structure** — where files lived in the source service.
2. **New path structure** — where files now live in the new service (from template mapping).
3. **Webpack alias changes** — old aliases → new aliases (if the template uses different conventions).

### Workflow

1. **Scan** — grep every copied file for imports referencing old paths. Include `.js`, `.jsx`, `.css`, `.scss`, test files, config files.
2. **Check Webpack aliases** — read the new service's `webpack.config.js` `resolve.alias`. Map old aliases to new ones.
3. **Build rewrite map** — explicit table: old path/import → new path/import → files affected. Rules:
   - Update relative paths to match new directory structure.
   - Update Webpack alias references if alias names changed.
   - Jest: update `jest.mock()` paths and module name mapper entries.
4. **Dry-run** — present all changes as diffs. **Wait for user confirmation.**
5. **Apply** — rewrite each import, preserving whitespace and comments.
6. **Handle edge cases** — see table below.
7. **Verify** — build + lint + test the new service. Grep for any old paths remaining.
8. **Output summary** — files modified, imports rewritten, remaining manual fixes.

### Edge Cases

| Case | Resolution |
|------|-----------|
| Webpack `resolve.alias` differs between services | Map old alias → new alias in rewrite map |
| `jest.moduleNameMapper` in package.json or jest.config | Update mapper entries for new paths |
| Playwright test selectors referencing old service URLs | Update base URLs to new service |
| `process.env.*` variable names changed | Search-replace env var names in JS files |

| Shared utility copied to different path | Update all internal references to new location |
| Hardcoded API paths (e.g., `/api/original-service/...`) | Update to new service's API prefix |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/teragroh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
