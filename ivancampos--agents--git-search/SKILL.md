---
name: git-search
description: List recent git commits across all repos under ~/Code (excluding .build checkouts). Use when the user asks to show last commits, recent history, or commits across all projects in ~/Code. Use when this capability is needed.
metadata:
  author: ivancampos
---

# Git Search

## Overview
Discover git repositories under `~/Code` and list recent commits for each one.

## Quick Start

```bash
scripts/list_commits.sh --count 10
```

## Tasks

### List recent commits across repos
- Run `scripts/list_commits.sh --count <N>`.
- Excludes `.build` checkouts.
- Prints a per-repo header and the last N commits.

## Resources

### scripts/
- `list_commits.sh`: Finds repos under `~/Code` and prints recent commits.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ivancampos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
