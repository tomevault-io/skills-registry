---
name: releasenotes
description: Generate formatted changelogs from git history since the last release tag. Use when preparing release notes that categorize changes into breaking changes, features, fixes, and other sections. Use when this capability is needed.
metadata:
  author: openhands
---

Generate a changelog for all changes from the most recent release until now.

## Steps
1. Find the most recent release tag using `git tag --sort=-creatordate`
2. Get commits and merged PRs since that tag
3. Look at previous releases in this repo to match their format and style
4. Categorize changes into sections: Breaking Changes, Added, Changed, Fixed, Notes
5. Focus on user-facing changes (features, important bug fixes, breaking changes)
6. Include PR links and contributor attribution

## Output
Present the changelog in a markdown code block, ready to copy-paste into a GitHub release.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openhands) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
