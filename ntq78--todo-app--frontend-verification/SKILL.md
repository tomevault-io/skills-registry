---
name: frontend-verification
description: Use when verifying frontend build or running type checks
metadata:
  author: ntq78
---

# Frontend Verification Skill

## Prerequisites

Before running any PNPM scripts (including this one), you MUST verify the script exists in package.json. See the **general-pnpm-scripts** skill for the verification workflow.

## Purpose

Run linting and TypeScript compilation checks to verify frontend code integrity without building the full application.

## When to Run

- After refactoring code
- After adding new features
- Before making commits
- When investigating type errors
- As a quick verification during development

## What It Does

Executes:

1. ESLint with `--max-warnings=0` (strict linting)
2. `tsc --noEmit` (TypeScript type checking without emitting files)

This is faster than a full build for verification purposes.

## Related

- Verification commands: See `.claude/rules/verification.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
