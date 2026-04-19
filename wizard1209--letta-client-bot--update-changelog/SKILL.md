---
name: update-changelog
description: Updates notes/changelog.md based on git history. Use when user says "update changelog", "changelog entry", "release version", "release X.Y.Z", or runs /update-changelog. Use when this capability is needed.
metadata:
  author: wizard1209
---

# Update Changelog

Read `notes/changelog.md` first to load the changelog rule (`.claude/rules/changelog.md`), then follow it.

## Workflow

1. Find baseline: `git log -1 --format="%H" -- notes/changelog.md`
2. Review commits: `git log <hash>..HEAD --oneline --no-merges`
3. Review files: `git diff <hash>..HEAD --name-only`
4. Draft entries following the changelog rule
5. Get user approval
6. Add to `[Latest additions]` section

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wizard1209) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
