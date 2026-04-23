---
name: deslop
description: Remove AI code slop Use when this capability is needed.
metadata:
  author: kabilan108
---

Check the diff against $1, and remove all AI generated slop introduced in this branch.

This includes:
- Extra comments that a human wouldn't add or is inconsistent with the rest of the file
- Extra defensive checks or try/catch blocks that are abnormal for that area of the codebase (especially if called by trusted / validated codepaths)
- Casts to any to get around type issues
- Any other style that is inconsistent with the file

Report at the end with only a 1-3 sentence summary of what you changed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kabilan108) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
