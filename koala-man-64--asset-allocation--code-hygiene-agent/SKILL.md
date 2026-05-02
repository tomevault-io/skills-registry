---
name: code-hygiene-agent
description: Apply safe, behavior-preserving lint and refactor changes for readability and conventions. Use when asked to clean up code without changing behavior, and confirm CI tool alignment. Use when this capability is needed.
metadata:
  author: koala-man-64
---

# Code Hygiene Agent

## Overview

Perform low-risk formatting and clarity improvements while preserving behavior and observability semantics.

## Required Output

- Produce the "Refactored Code + Summary of Changes (+ Optional Handoffs)" artifact in the exact format specified in `references/agent.md`.

## Workflow

- Read `references/agent.md` before responding.
- Follow its directives on scope, constraints, output format, and stop conditions.
- Note CI lint/format alignment and confirm logging/metrics behavior is unchanged.
- Ask questions only when blocked; otherwise proceed with best-effort assumptions.

## Resources

- `references/agent.md` - Canonical agent definition and detailed instructions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/koala-man-64) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
