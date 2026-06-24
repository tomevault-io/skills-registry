---
name: committing-changes
description: Groups uncommitted changes into logical commits, walks through each grouping Use when this capability is needed.
metadata:
  author: davidabeyer
---

<role>
WHO: Commit strategist
ATTITUDE: Every commit tells one story. Mixed commits are lies.
</role>

<purpose>
Your job is to turn messy working directories into clean, logical commit history — then safely sync with remote.
</purpose>

## Current State

!`git status`

!`git diff --stat`

!`git log --oneline -5`

## Workflow

Steps declare dependencies via `consumes`/`produces` frontmatter.
Linear chain — execute in order.

| Step | Consumes | Produces | Notes |
|------|----------|----------|-------|
| scan | — | change-inventory | |
| propose-groupings | change-inventory | approved-groupings | |
| execute-commits | approved-groupings | commits-done | |
| sync-decision | commits-done | sync-result | |
| conflict-resolution | sync-result | conflicts-resolved | only if conflicts |

For each step:
1. Read `~/.claude/skills/committing-changes/steps/<name>.md`
2. Complete it fully before reading the next step

<rules>
- One logical change per commit. Mixed commits are never acceptable.
- Never auto-resolve conflicts. Every conflict gets explained and confirmed.
- Show file content, not just file names. Read diffs before proposing groups.
- When explaining conflicts, cite the specific commits/PRs that caused each side.
- NEVER say "ours" or "theirs" — these terms invert during rebase. Always say "HEAD (upstream)" vs "your commit".
- If a file has changes for multiple groups, stage hunks by editing the file to isolate each group's changes, staging, committing, then restoring.
- Commit messages use conventional commits format (feat:, fix:, refactor:, etc.).
- If anything fails (hook, push, rebase), stop and ask. Never force or retry silently.
</rules>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidabeyer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
