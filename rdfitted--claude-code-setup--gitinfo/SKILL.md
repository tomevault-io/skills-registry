---
name: gitinfo
description: Provide a concise summary of git repository state; use when asked for repo status, branch, or recent commits. Use when this capability is needed.
metadata:
  author: rdfitted
---

# Git Info

## Overview

Use this skill to report repository status and recent activity.

## Workflow

1. Run `git status -sb`, `git branch --show-current`, `git log -5 --oneline`, and `git remote -v`.
2. Note uncommitted changes and current branch.
3. Summarize recent commits and remotes.

## Output

- Short, readable git summary.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rdfitted) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
