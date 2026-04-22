---
name: general
description: General execution skill for docs-first implementation, error handling, and repository-wide consistency Use when this capability is needed.
metadata:
  author: sujan-6905
---

# General Skill

## Default Behavior

Use this skill when the task spans multiple areas or no narrower skill is a better fit.

## Prefer Another Skill When

- the task is clearly frontend-only, backend-only, devops-only, or docs-only
- the task is specifically about subagent orchestration or memory usage

## Core Operating Rules

1. Read enough context before changing anything.
2. Prefer official docs over memory or guesswork.
3. Keep changes focused and internally consistent.
4. Update user-facing docs when behavior or setup changes.
5. Verify the result with the best checks available.

## Research Rule

- Use `context7` for official documentation.
- Use `gh_grep` for implementation patterns.
- Use `webfetch` when the needed documentation is outside MCP.

## Repository Standards

- Keep structure simple.
- Avoid unnecessary new folders and files.
- Maintain naming consistency.
- Keep config, prompts, and docs aligned.

## Environment Rule

- Never read or edit `.env` directly.
- When environment requirements change, create or update `.env.example`.
- Assume the documented keys also exist in `.env`.

## Session Rule

If prior session context exists, use it to continue rather than restarting from scratch.

## Done Criteria

- the task is implemented, not merely analyzed
- related docs are current
- configuration drift has not been introduced

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sujan-6905) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
