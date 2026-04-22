---
name: cleanup
description: Cleanup skill for post-change validation, consistency checks, and finishing discipline Use when this capability is needed.
metadata:
  author: sujan-6905
---

# Cleanup Skill

## Purpose

Use this skill after implementation to make sure the result is coherent, validated, and ready to hand back.

## Do Not Use This Skill For

- replacing implementation work that still has obvious missing pieces
- skipping validation because the change seems small

## Cleanup Workflow

1. Check diagnostics in changed files.
2. Run the strongest relevant validation available.
3. Remove accidental duplication or stale references created during the change.
4. Verify related docs and examples were updated.
5. Confirm no prohibited behavior was introduced.

## Validation Targets

- syntax and type errors
- linting errors
- broken config references
- outdated model or tool names after renames
- missing `.env.example` updates when env requirements changed

## Rules

- If cleanup finds a real issue caused by the task, fix it before finishing.
- Do not treat validation as optional.
- Report what was verified and what could not be run.

## Environment Rule

- verify environment-variable documentation through `.env.example`
- do not use `.env` as part of cleanup

## Done Criteria

- changed files are clean according to available diagnostics
- related docs and examples are synchronized
- the final report reflects real verification

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sujan-6905) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
