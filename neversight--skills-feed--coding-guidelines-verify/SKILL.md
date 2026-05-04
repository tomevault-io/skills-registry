---
name: coding-guidelines-verify
description: Verify changes follow nearest-scoped AGENTS.md rules: group changed files by nested scope, auto-fix formatting, run lint/tests, and report violations. Use when this capability is needed.
metadata:
  author: neversight
---

# Coding guidelines verifier

## Goal
Validate that changes follow the **nearest nested** `AGENTS.md`:
- default: **changed files only**
- default: **auto-fix formatting** before lint/tests
- monorepo-aware: each module’s `AGENTS.md` is the source of truth for that scope

## Workflow (checklist)
1) Collect changed files (staged + unstaged + untracked).
2) For each changed file, find the nearest parent `AGENTS.md`.
   - If a file has no scoped `AGENTS.md`, report it (suggest running `coding-guidelines-gen`).
3) Parse the `codex-guidelines` block (schema: `references/verifiable-block.md`).
4) Run, per scope:
   - format (auto-fix) -> lint -> tests
   - apply simple forbid rules (globs/regex) from the block
5) Produce a short compliance report (template: `references/report-template.md`).

## Automation
Use `scripts/verify_guidelines.py` to group scopes, run commands, and report results.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
