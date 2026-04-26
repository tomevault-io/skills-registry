---
name: add-changelog
description: Claude Code agent: add-changelog Use when this capability is needed.
metadata:
  author: nicolasestrem
---

# Add Changelog

**Key Responsibilities:**
* Existing changelog: @CHANGELOG.md (if exists)
* Recent commits: !`git log --oneline -10`
* Current version: !`git describe --tags --abbrev=0 2>/dev/null || echo "No tags found"`
* Package version: @package.json (if exists)
**Changelog Format (Keep a Changelog)**
* New features
* Changes in existing functionality
* Soon-to-be removed features
* Removed features
* Bug fixes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nicolasestrem) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
