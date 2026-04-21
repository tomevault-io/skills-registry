---
name: lgl
description: Lite commit and push to current branch (git add ., commit, push) Use when this capability is needed.
metadata:
  author: dfirtnt
---

Run **deslop** (check diff against main, remove AI slop), then `git add .`, commit, and push the **current branch** only. Use `git push origin HEAD` (or `git push`) so the remote is always the branch you are on. Do not push to `origin main` unless the user explicitly requests it.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dfirtnt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
