---
name: project-mgmt
description: > Use when this capability is needed.
metadata:
  author: superbenefit
---

# Project Management

Spec-driven development with filesystem as persistent memory.

## Core Principle

Context window is volatile. Filesystem is persistent.
Anything important gets written to disk.

## Workflow Phases

Each phase has its own reference file. Read the appropriate one:

| Phase | When | Reference |
|-------|------|-----------|
| Start | Initialize project | `references/start.md` |
| Specify | Define requirements | `references/specify.md` |
| Plan | Create approach | `references/plan.md` |
| Implement | Execute steps | `references/implement.md` |
| PR | Create pull request | `references/pr.md` |
| Sync | Update GitHub | `references/sync.md` |

## Files

| File | Purpose |
|------|---------|
| spec.md | Requirements (what & why) |
| plan.md | Implementation steps + session context |
| findings.md | Research, decisions |
| progress.md | Session log, errors |

Location: `.project/{issue#}/`
Templates: `assets/`

## Key Rules

1. Create plan.md before starting work
2. Save findings after every 2 search/view operations
3. Log all errors to progress.md
4. Re-read plan.md before major decisions
5. Run the pm-bookkeeper agent in the background for file updates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/superbenefit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
