---
name: code-review
description: Systematic multi-perspective code review with consistent quality gates. Use when this capability is needed.
metadata:
  author: salavender
---

# Code Review Skill

## Overview

A systematic approach to code review that moves beyond "it looks good" to rigorous quality verification. This skill provides specific checklists and procedures for different review types.

## When To Use

- **Self-Review**: Before submitting a PR or finishing `/work`
- **Peer Review**: When reviewing another agent's or human's code (`/resolve_pr`)
- **Plan Review**: When validating an implementation plan (`/plan_review`)

## Instrumentation

```bash
# Log usage when using this skill
./scripts/log-skill.sh "code-review" "manual" "$$"
```

## What do you want to do?

1. **Security Review** (Auth, RLS, Input) → `workflows/security-pass.md`
2. **Performance Review** (Database, Re-renders) → `workflows/performance-pass.md`
3. **Architecture Review** (State, Data Flow) → `workflows/architecture-pass.md`
4. **General Quality Check** → `checklists/pre-merge.md`

## Key Principles

- **Review in Passes**: Don't check everything at once. Do a security pass, then a performance pass, etc.
- **Reference Patterns**: Always check against `docs/solutions/patterns/critical-patterns.md`.
- **Verify, Don't Guess**: If you see a potential issue, verify it with a quick test or script.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/salavender) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
