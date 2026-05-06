---
name: preflight
description: Session startup checklist to load context and check for stale state. Use this skill at the start of any development session, when resuming after a break, or before deployment tasks. Use when this capability is needed.
metadata:
  author: neversight
---

# Preflight Workflow

Run this at the **start of any development session** to load context and catch issues early.

## When This Skill Activates

- Start of a new conversation
- Resuming after a long break
- Before any deployment task
- When user mentions context seems forgotten
- When project state is unclear

## Preflight Checklist

### 1. Read Lessons Learned

Review lessons learned for critical rules and past mistakes (paths are repo-root relative):

```bash
cat ./.claude/rules/lessons.md
```

### 2. Check Project Status

Read the current status to understand what's in progress:

```bash
cat STATUS.md
```

### 3. Review Recent Decisions

Check if there are any architectural decisions that affect current work:

```bash
cat DECISIONS.md | head -100
```

### 4. Check for Uncommitted Changes

Ensure the workspace is clean:

```bash
git status
```

### 5. Report Context Summary

After reading the above, provide a brief summary to the user:

- Current project status
- Any warnings or blockers
- Recent changes that may affect current work
- Ready to proceed or not

## Anti-Patterns to Avoid

- Starting work without reading lessons learned
- Assuming previous session context is remembered
- Ignoring uncommitted changes
- Skipping deployment verification before contract work

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
