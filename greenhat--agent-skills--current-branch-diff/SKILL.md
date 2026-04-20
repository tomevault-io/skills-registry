---
name: current-branch-diff
description: | Use when this capability is needed.
metadata:
  author: greenhat
---

# Current Branch Diff (ignore artifacts)

## Quick start

1. Print the parent branch:
   - `git parent-branch`
2. Review the diff (merge-base comparison) while ignoring `.wat`, `.hir`, `.masm`, and `.lock` files:
   - `parent="$(git parent-branch)"`
   - `git diff --stat "$parent"...HEAD -- . ':(exclude)*.wat' ':(exclude)*.hir' ':(exclude)*.masm' ':(exclude)*.lock'`
   - `git diff "$parent"...HEAD -- . ':(exclude)*.wat' ':(exclude)*.hir' ':(exclude)*.masm' ':(exclude)*.lock'`

## Notes

- Prefer `"$parent"...HEAD` (three dots) to compare against the merge-base with the parent branch.
- If you want a quick file list first:
  - `git diff --name-only "$parent"...HEAD -- . ':(exclude)*.wat' ':(exclude)*.hir' ':(exclude)*.masm' ':(exclude)*.lock'`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/greenhat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
