---
name: fusion-arch
description: Compare architecture patterns and select the most suitable; use for system design decisions. Use when this capability is needed.
metadata:
  author: rdfitted
---

# Fusion Architecture

## Overview

Use an F-thread: three workers propose different architecture patterns in isolated worktrees, and a queen evaluates tradeoffs.

## Inputs

- System or feature description

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

- Worker A: monolith design
- Worker B: microservices design
- Worker C: modular monolith design

## Queen Prompt Outline

- Evaluate complexity, scalability, team fit, and operational cost

## mprocs Launch

```bash
mprocs --config .hive/mprocs.yaml
```

## Output

- Architecture comparison and selected approach

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rdfitted) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
