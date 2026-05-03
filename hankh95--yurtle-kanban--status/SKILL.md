---
name: status
description: Show kanban board status, agent workload, and work distribution Use when this capability is needed.
metadata:
  author: hankh95
---

# Kanban Status

Show the current state of the kanban board and work distribution.

## Overview

```bash
# Board statistics
yurtle-kanban stats

# Current git branch
git branch --show-current

# Recent commits
git log --oneline -5
```

## In Progress

```bash
yurtle-kanban list --status in_progress
```

## Ready to Start

```bash
yurtle-kanban list --status ready --limit 10
```

## In Review

```bash
yurtle-kanban list --status review
```

## Recently Completed

```bash
yurtle-kanban list --status done --limit 5
```

## Blocked Items

```bash
yurtle-kanban list --status blocked
```

## Summary

Provide a brief summary of:
- What's currently being worked on
- What's ready to pick up (highest priority)
- What's waiting for review
- Any blocked items that need attention
- Recommendations for next steps

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hankh95) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
