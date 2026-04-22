---
name: refactor-clean
description: description: Safely identify and remove dead code with test verification. Invokes refactor-cleaner agent. Use when cleaning unused code, removing duplicates, or reducing bundle size. Use when this capability is needed.
metadata:
  author: ihj04982
---
---
name: refactor-clean
description: Safely identify and remove dead code with test verification. Invokes refactor-cleaner agent. Use when cleaning unused code, removing duplicates, or reducing bundle size.
disable-model-invocation: true
---

# Refactor Clean Command

This command invokes the **refactor-cleaner** agent to identify and safely remove dead code, duplicates, and unused exports.

## What This Command Does

1. **Analyze** - Run knip, depcheck, ts-prune to find unused code
2. **Categorize** - SAFE (utilities), CAUTION (components), DANGER (entry points)
3. **Propose safe deletions only** - No risky removals
4. **Test before/after** - Full test suite run before each deletion
5. **Track** - Document deletions in DELETION_LOG.md

## When to Use

Use `/refactor-clean` when:
- Cleaning up unused code
- Removing duplicates
- Consolidating codebase
- Reducing bundle size
- After major refactoring to prune orphaned code

## How It Works

The refactor-cleaner agent runs dead code detection tools, generates a report, and applies deletions only when tests pass. It never deletes without running tests first.

## Related

- Agent: `agents/refactor-cleaner.md`
- Tools: knip, depcheck, ts-prune

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ihj04982) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
