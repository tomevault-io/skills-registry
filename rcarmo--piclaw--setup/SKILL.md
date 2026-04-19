---
name: setup
description: Initialize a new project in the workspace. Scaffolds files, Makefile, git, and AGENTS.md for the chosen project type. Use when this capability is needed.
metadata:
  author: rcarmo
---

# Setup

Initialize a new project in this workspace.

## Steps

1. Ask what kind of project to create (TypeScript library, web app, CLI tool, API server, etc.)
2. Scaffold with bun:
   - `bun init` for a basic TypeScript project
   - Or appropriate scaffolding for the chosen framework
3. Create a `Makefile` with targets: install, lint, test, check, clean
4. Initialize git: `git init && git add -A && git commit -m "Initial commit"`
5. Create `AGENTS.md` in the project root with project-specific context
6. Create `.pi/skills/` for any project-specific skills

## Notes

- Always use bun over npm/yarn
- Default to TypeScript unless told otherwise
- The workspace persists across container restarts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rcarmo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
