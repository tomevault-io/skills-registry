---
name: bump-version
description: >- Use when this capability is needed.
metadata:
  author: wesbos
---

# Bump Version

## Workflow

1. **Read current version** from `package.json`
2. **Determine bump type** — ask the user if not specified:
   - `patch` (1.0.5 → 1.0.6)
   - `minor` (1.0.5 → 1.1.0)
   - `major` (1.0.5 → 2.0.0)
3. **Update both files** with the new version:
   - `package.json` → `"version": "X.Y.Z"`
   - `manifest.json` → `"version": "X.Y.Z"`
4. **Commit** the version change:
   ```
   git add package.json manifest.json
   git commit -m "bump version to X.Y.Z"
   ```
5. **Tag** the commit:
   ```
   git tag vX.Y.Z
   ```
6. Report the new version and remind the user to `git push --tags` when ready.

## Rules

- Always keep `package.json` and `manifest.json` versions in sync.
- Use the `v` prefix for git tags (e.g. `v1.1.0`).
- Do **not** push automatically — let the user decide when to push.

---
> Source: [wesbos/JSON-Alexander](https://github.com/wesbos/JSON-Alexander) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-20 -->
