---
name: committing-code
description: Smart git commits with logical grouping. Use when user says "commit", "commit changes", "save changes", "create commit", "bundle commits", "git commit", or wants to commit their work. Use when this capability is needed.
metadata:
  author: neversight
---

# Smart Commit

Group changed files logically into focused, atomic commits.

**No agents. Inline analysis for speed.**

## Step 1: Gather State

Run in parallel:

```bash
git status --porcelain
git diff --name-status HEAD
git log --oneline -8
```

**If no changes:** Say "Nothing to commit" → stop.

## Step 2: Analyze & Present

Group files by: feature (impl+tests), fix (bug+test), refactor, docs, config

Match commit style from recent history.

**Present proposed commits:**

```
Proposed commits:

1. feat: add user validation
   - src/validate.ts
   - src/validate_test.ts

2. docs: update README
   - README.md
```

## Step 3: Execute

For each group, run git add + commit.

User will be prompted to approve each write operation (git add/commit not pre-allowed).

## Step 4: Summary

Show `git status` and list commits created.

---

**Execute now.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
