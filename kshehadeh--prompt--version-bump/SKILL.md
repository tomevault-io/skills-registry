---
name: version-bump
description: Version bumps are performed by scripts/release.ts from commits since the last v* tag; do not edit package.json manually for releases. Use when this capability is needed.
metadata:
  author: kshehadeh
---

# Version bump

The app version in **`apps/web/package.json`** is bumped only through the release script:

- Run **`bun run release`** from the monorepo root (see `.agents/skills/release` and [`scripts/release.ts`](../../../scripts/release.ts)).
- **Patch** unless any commit since the last `v*` tag has a subject matching `feat:`, `feat(scope):`, or `feature:` (case-insensitive for `feature:`), in which case **minor**.
- There is no separate “stamp changelog JSON” step; GitHub Release notes are generated in CI from `git log`.

If you are not ready to cut a release, do not change the published version string in `package.json`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kshehadeh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
