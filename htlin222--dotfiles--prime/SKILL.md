---
name: prime
description: Load codebase context by analyzing structure and README. Use when starting a new session. Use when this capability is needed.
metadata:
  author: htlin222
---

# Prime

This command loads essential context for a new agent session by examining the codebase structure and reading the project README.

## When to use

- When starting a new agent session on a project
- When you need to quickly understand an unfamiliar codebase
- When onboarding to a new project or repository
- When you need a concise project overview

## Instructions

- Run `git ls-files` to understand the codebase structure and file organization
- Read the README.md to understand the project purpose, setup instructions, and key information
- Provide a concise overview of the project based on the gathered context

## Context

- Codebase structure git accessible: !`git ls-files`
- Codebase structure all: !`eza . --tree`
- Project README: @README.md
- Documentation:
  - @ai_docs/cc_hooks_docs.md
  - @ai_docs/uv-single-file-scripts.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/htlin222) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
