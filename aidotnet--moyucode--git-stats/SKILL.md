---
name: git-stats
description: 分析Git仓库统计信息，包括提交、贡献者和代码变更。 Use when this capability is needed.
metadata:
  author: aidotnet
---

# Git Stats Tool

## Description
Analyze Git repository statistics including commit history, contributor activity, and code changes.

## Trigger
- `/git-stats` command
- User needs repository analysis
- User wants commit statistics

## Usage

```bash
# Repository overview
python scripts/git_stats.py

# Contributor stats
python scripts/git_stats.py --contributors

# Commit history
python scripts/git_stats.py --commits --since "2024-01-01"

# File changes
python scripts/git_stats.py --files --top 10
```

## Tags
`git`, `stats`, `repository`, `commits`, `analysis`

## Compatibility
- Codex: ✅
- Claude Code: ✅

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aidotnet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
