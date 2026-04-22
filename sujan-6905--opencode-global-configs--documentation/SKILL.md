---
name: documentation
description: Documentation skill for concise, high-signal project docs that stay synchronized with implementation Use when this capability is needed.
metadata:
  author: sujan-6905
---

# Documentation Skill

## Use This Skill For

- README maintenance
- setup and usage instructions
- documenting behavior changes, risks, and operational notes
- keeping implementation and docs synchronized

## Do Not Use This Skill For

- implementation-only tasks that do not change behavior, setup, or usage
- sprawling low-signal docs that the README does not need

## Core Rules

- Keep docs concise and useful.
- Prefer updating the root README before creating new markdown files.
- Add new docs only when the README would become unclear or unmanageably large.
- Document non-obvious setup, risks, and breaking changes.

## After Significant Changes

Update documentation for:

1. new commands or workflows
2. model or provider changes
3. MCP or integration changes
4. environment variable requirements in `.env.example`
5. behavioral risks or usage caveats

## Bug Fix Notes

For important fixes, capture:

1. the user-visible issue
2. the cause
3. the fix
4. any follow-up needed

## Environment Rule

- Do not instruct agents to read `.env`.
- Document variables in `.env.example`.
- State that `.env.example` mirrors the keys expected in the local `.env`.

## Done Criteria

- docs reflect the current system, not the previous one
- setup steps are accurate
- warnings and caveats are visible enough to matter

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sujan-6905) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
