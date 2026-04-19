---
name: release-notes
description: Generate release notes from git log between tags Use when this capability is needed.
metadata:
  author: guzus
---

Generate release notes for the next release.

1. Run `git tag --sort=-v:refname | head -5` to find recent tags
2. Run `git log <last-tag>..HEAD --oneline` to get commits since last release
3. Group commits by type (feat, fix, docs, style, refactor, test, chore) based on conventional commit prefixes
4. Output a markdown changelog:
   - **New Features** — feat: commits
   - **Bug Fixes** — fix: commits
   - **Other Changes** — everything else
5. Skip merge commits and CI-only changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guzus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
