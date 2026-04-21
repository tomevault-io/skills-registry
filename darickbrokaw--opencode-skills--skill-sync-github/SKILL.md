---
name: skill-sync-github
description: Synchronizes OpenCode skills with GitHub repository Use when this capability is needed.
metadata:
  author: darickbrokaw
---

# Sync Skills and Plugins

## Description
Synchronizes local OpenCode skills and plugins with the GitHub repository. Fetches remote changes, commits local changes, and pushes to GitHub.

## Usage
```
skill sync-skills
```

## What it does
1. Fetches latest changes from GitHub
2. Commits any local changes (skills, plugins, opencode.jsonc)
3. Pushes to GitHub
4. Pulls remote changes with rebase

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darickbrokaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
