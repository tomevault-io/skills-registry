---
name: shre-coding-rules
description: Use when a repository needs one shared coding contract that multiple models or agents should follow. Trigger for requests about shared engineering rules, AGENTS.md baselines, CODEX/CLAUDE/GEMINI rule alignment, repo-wide coding standards, architecture guardrails, milestone-aware implementation discipline, or converting existing local rules into a reusable skill or plugin.
metadata:
  author: Nirpat3
---

# Shre Coding Rules

## Overview

This skill establishes one source of truth for coding rules across multiple models and agent entry files.
Use it when you need to create, adapt, or enforce a shared engineering contract without copying the same rules into every model-specific file.

## Workflow

1. Identify the canonical rules file for the repository.
2. Keep that file as the single source of truth.
3. Point `AGENTS.md`, `CODEX.md`, `CLAUDE.md`, `GEMINI.md`, or equivalent entry files back to the canonical rules file.
4. Add only repo-specific deltas in local docs; do not duplicate the full ruleset unless there is a hard integration requirement.
5. When packaging for Codex, expose the rules through a plugin skill rather than scattering rule copies across plugins or repos.

## What To Produce

- A canonical rules document, usually `docs/shared-coding-rules.md`
- Root entry files that point to it
- Optional plugin packaging when the rules should be installable or reusable across repositories
- Optional reference files with project-specific examples or adaptations
- For new projects, a repeatable install path so the rules are applied to every repo and every model entry file

## Rules For This Skill

- Prefer one authoritative rules file over repeated copies.
- Keep model-specific files short; they should route, not restate.
- Preserve architecture boundaries, safety rules, and verification discipline.
- Keep the skill lean and put detailed examples into `references/`.
- If converting repo-specific rules into a general skill, separate the generic baseline from repo-specific examples.

## When To Read References

- Read `references/adoption-template.md` when creating a fresh shared-rules setup in another repository.
- Read `references/agentic-hardware-example.md` when you want a concrete example derived from a real layered system.

## Output Pattern

Use this shape:

```text
repo-root/
├── AGENTS.md
├── CODEX.md
├── CLAUDE.md
├── GEMINI.md
└── docs/
    └── shared-coding-rules.md
```

Model-specific files should say, in effect:

- start with `AGENTS.md`
- then read `docs/shared-coding-rules.md`
- follow the shared contract before editing code

## Packaging Pattern

When packaging as a Codex plugin:

```text
plugins/<plugin-name>/
├── .codex-plugin/plugin.json
└── skills/
    └── shre-coding-rules/
        ├── SKILL.md
        ├── agents/openai.yaml
        └── references/
```

## Resources

This skill uses `references/` only. Keep examples and adaptation guides there so the main skill stays compact.

---
> Source: [Nirpat3/ShreCodingRules](https://github.com/Nirpat3/ShreCodingRules) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
