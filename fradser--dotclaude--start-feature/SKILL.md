---
name: start-feature
description: Starts working on a new feature branch using git-flow. This skill should be used when the user asks to "start a feature", "create feature branch", "begin new feature", "git flow feature start", or wants to start a new feature. Use when this capability is needed.
metadata:
  author: fradser
---

## Workflow Execution

**Launch a general-purpose agent** that executes all phases in a single task.

**Prompt template**:
```
Execute the start-feature workflow.

## Pre-operation Checks
Verify working tree is clean per `${CLAUDE_PLUGIN_ROOT}/references/invariants.md`.

## Phase 1: Start Feature
**Goal**: Create feature branch using git-flow-next CLI.
1. Run `git flow feature start $ARGUMENTS`
2. Push the branch to origin: `git push -u origin feature/$ARGUMENTS`
```

**Execute**: Launch a general-purpose agent using the prompt template above

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fradser) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
