---
name: repo-scaffold-pro
description: Use when the request requires create a senior-team-grade repository scaffold aligned to the chosen stack, including docs, checks, design system bootstrap, and a first vertical slice.
metadata:
  author: ricardohg1998-create
---

# Repo Scaffold Pro

## Do not use when
- The request is unrelated to this domain or requires a different specialized skill.
- The user asks only for high-level discussion without applying this workflow.
- Another skill has a tighter, more specific trigger for the same request.

## Example user requests
- "Apply repo scaffold pro to improve this feature."
- "Use repo scaffold pro and give me the concrete deliverables."
- "Can you run a full repo scaffold pro pass on this repo?"
- "I need step-by-step execution using repo scaffold pro."
## Goal
Create a runnable scaffold that forces depth (not UI-only demos).

## When to use
- After stack selection.
- Empty folder start.

## Minimal inputs (ask only if missing)
- Selected stack.
- Package manager (or choose and document).

## Procedure (MUST)
1) Ensure `.agent/` at repo root.
2) Create topology (`web/`, `api/`, `docs/`).
3) Copy templates into `docs/`.
4) Bootstrap tokens + base UI.
5) Add scripts for checks.
6) Implement one vertical slice with persistence + full states.
7) Update walkthrough + decision log.

## Outputs (MUST produce)
- Runnable scaffold.
- Tokens + base UI.
- One vertical slice + proof plan.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ricardohg1998-create) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
