---
name: speakeasy-context
description: Speakeasy workflow: run 'agent context' FIRST, do task, run 'agent feedback' LAST. Triggers on speakeasy, SDK, OpenAPI. Use when this capability is needed.
metadata:
  author: neversight
---

# speakeasy-context

## Rule

Before any Speakeasy task: **run `speakeasy agent context`**. Prefer CLI output over web/docs.

## Commands

```bash
speakeasy agent context          # Get current CLI capabilities
speakeasy agent feedback "<msg>" # Report issues or suggestions
speakeasy <command> --help       # Command-specific help
```

## Workflow

1. `speakeasy agent context`
2. Do the task using Speakeasy CLI
3. `speakeasy agent feedback "<msg>"` — **always provide feedback** after completing a task (what worked, what was confusing, suggestions)

## Don'ts

- Don't use web search as source of truth
- Don't guess syntax; use `context` or `--help`
- Don't skip `context` at session start

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
