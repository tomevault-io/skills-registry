---
name: fusion-test
description: Compare testing strategies and choose the best coverage plan; use when deciding how to test a feature or fix. Use when this capability is needed.
metadata:
  author: rdfitted
---

# Fusion Test

## Overview

Use an F-thread: three workers propose test strategies. The queen selects the strongest coverage plan.

## Inputs

- Feature or fix description

## Workflow

1. Verify `git` and `mprocs`.
2. Create session variables and worktrees.
3. Write `tasks.json`, worker prompts, and queen prompt.
4. Launch mprocs.

## Worktree Commands

```bash
git worktree add "{WORKTREE_ROOT}/impl-a" -b fusion/{SESSION_ID}/impl-a
git worktree add "{WORKTREE_ROOT}/impl-b" -b fusion/{SESSION_ID}/impl-b
git worktree add "{WORKTREE_ROOT}/impl-c" -b fusion/{SESSION_ID}/impl-c
```

## Worker Prompt Outline

- Worker A: unit-test heavy
- Worker B: integration and contract tests
- Worker C: end-to-end and regression focus

## Queen Prompt Outline

- Compare coverage, effort, and reliability

## mprocs Launch

```bash
mprocs --config .hive/mprocs.yaml
```

## Output

- Selected testing strategy and test list

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rdfitted) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
