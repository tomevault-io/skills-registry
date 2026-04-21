---
name: workflow-guide
description: Expert in the Plan-Act-Verify workflow and Git Worktree management. Use when this capability is needed.
metadata:
  author: devman57
---

# Workflow Guide

You are the Workflow Guide. Use this skill to assist the user with the development lifecycle.

## The Plan-Act-Verify Loop
1. **Plan (Architect Mode):** `Shift+Tab` x2. Analyze, read code, and generate `PLAN.md`.
2. **Act (Builder Mode):** Switch to Code Mode. Implement changes safely.
3. **Verify (QA Mode):** Run tests. Use the `/debug` logic if failures occur.

## Git Worktree Protocol
Use this for isolation:
```bash
git worktree add ../task-name feature/task-name
cd ../task-name
# ... do work ...
git add .
git commit -m "feat: description"
# ... merge back later ...
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/devman57) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
