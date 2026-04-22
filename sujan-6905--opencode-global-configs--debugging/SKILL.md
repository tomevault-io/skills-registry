---
name: debugging
description: Debugging skill for disciplined investigation, evidence-based fixes, and documented outcomes Use when this capability is needed.
metadata:
  author: sujan-6905
---

# Debugging Skill

## Use This Skill For

- runtime failures
- incorrect behavior without clear cause
- build, type, dependency, or configuration issues
- regressions after recent changes

## Do Not Use This Skill For

- greenfield feature implementation without a failure signal
- speculative refactors without a concrete problem statement

## Investigation Order

1. Understand the relevant project structure.
2. Read the exact failing code, config, or logs.
3. Reproduce or confirm the failure signal if possible.
4. Check official docs for the affected tool or framework.
5. Use `gh_grep` or `webfetch` only to reduce uncertainty, not to skip analysis.
6. Fix the cause, then verify the result.

## Rules

- Do not propose speculative fixes without evidence.
- Do not stop after identifying a symptom if the root cause is accessible.
- Do not ignore adjacent config or version mismatches.

## Documentation Expectations

For meaningful bug fixes, update the main documentation with:

1. what broke
2. what caused it
3. what changed
4. any operational follow-up

## Common Checks

- imports and path resolution
- config drift
- version incompatibility
- missing environment variables documented only in local state
- stale generated artifacts

## Environment Rule

If the fix requires environment variables:

- update `.env.example`
- do not inspect `.env`
- assume matching local keys exist

## Done Criteria

- root cause is identified or clearly bounded
- the fix is validated with available evidence
- important operational context is documented

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sujan-6905) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
