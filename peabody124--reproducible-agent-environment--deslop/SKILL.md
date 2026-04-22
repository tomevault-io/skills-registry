---
name: deslop
description: > Use when this capability is needed.
metadata:
  author: peabody124
---

Review the diff against main and remove all AI-generated slop introduced in the
outstanding code changes, most recent commit, or branch based on user instructions.

Refer to the anti-patterns guide in `enforce-guidelines/references/anti-patterns.md`
for the full list of slop patterns to catch.

Common slop to remove:
- Extra comments that a human wouldn't add or that are inconsistent with the rest of the file
- Extra defensive checks or try/catch blocks that are abnormal for that area of the codebase (especially if called by trusted / validated codepaths)
- Casts to `Any` to get around type issues
- Placeholder patterns (TODOs without context, "Phase 1" comments, commented-out code)
- Over-engineering (abstractions for single-use code, unnecessary factories)
- Any other style that is inconsistent with the file

Report at the end with only a 1-3 sentence summary of what you changed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/peabody124) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
