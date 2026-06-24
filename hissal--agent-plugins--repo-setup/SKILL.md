---
name: repo-setup
description: > Use when this capability is needed.
metadata:
  author: Hissal
---

# Repo Setup Skill

A guided, interactive skill for setting up a new project repository from scratch.

## Overview

This skill walks the user through a series of upfront questions, then creates all files on disk
and displays a text summary of the structure. It always produces the full set of standard files
and wires up git (with optional remote and CI/CD).

---

## Step 1 — Ask Questions Upfront

Before creating anything, ask the user ALL of the following. You can use the `ask_user_input_v0`
tool to make this interactive, grouping related questions together (max 3 per call).

### Round 1 — Project basics
1. **Project name** — what's the repo/folder called?
2. **Short description** — one sentence describing what it does
3. **Primary language / stack** — e.g. Python, Node.js, Go, Rust, mixed, etc.

### Round 2 — Structure & remote
4. **Folder structure** — should Claude suggest a structure based on the stack, or does the user have something specific in mind?
5. **Git remote** — do they have a remote URL (GitHub/GitLab/etc.) to connect to?
6. **License** — MIT, Apache 2.0, GPL, proprietary/none, or ask later?

### Round 3 — Optional extras
7. **CI/CD** — do they want a basic CI/CD workflow file (e.g. GitHub Actions)?  
   - If yes: what should it do? (lint, test, build, deploy, or a combo)
8. **Any additional context** — anything else to note in CODEBASE.md (team conventions, external services, special constraints)?

Only proceed to Step 2 once you have answers to all required fields (name, description, stack).
The rest can use sensible defaults if the user skips them.

---

## Step 2 — Show a Plan

Before creating files, print a clear summary:

```
📁 Project: <name>
📝 Description: <description>
🛠  Stack: <stack>

Files to be created:
  <project-name>/
  ├── README.md
  ├── .gitignore
  ├── .editorconfig
  ├── CLAUDE.md
  ├── AGENTS.md
  ├── llm-guidelines.md
  ├── CODEBASE.md
  ├── CONVENTIONS.md
  ├── <suggested folder structure>
  └── [.github/workflows/ci.yml — if CI/CD selected]

Git: init locally [+ remote: <url> if provided]
```

Ask: "Looks good? I'll create everything now." — wait for confirmation before proceeding.

---

## Step 3 — Create Files on Disk

Use `bash_tool` to create the project. Work directory: wherever the user's session is, or `/home/claude/<project-name>/` if no path specified.

### 3a — Folder structure
Create the root folder and any subdirectories appropriate for the stack.
See `references/folder-structures.md` for stack-specific templates.

**Stack presets** — some stacks have dedicated reference files with extra setup steps:
- **Unity** → read `references/unity-preset.md` in full before creating any files. The root
  `.gitignore` goes in the Unity project root (next to `Assets/`). The three `_` folders are
  created inside `Assets/`. Do not create a new root folder.
- **C# / .NET** → read `references/dotnet-preset.md`. Ask the user which project type they need
  (Console, Web API, or Class Library) before scaffolding, then use the matching template.

### 3b — Standard config files

#### README.md
```markdown
# <project-name>

<description>

## Getting Started

_TODO: Add setup instructions._

## Architecture

See [CODEBASE.md](./CODEBASE.md) for a full architecture overview.

## AI / Agent Guidelines

See [CLAUDE.md](./CLAUDE.md) for AI assistant context and conventions.
```

#### .gitignore
Generate an appropriate `.gitignore` for the stack. For generic/unknown stacks, include:
- OS files: `.DS_Store`, `Thumbs.db`
- Editor: `.vscode/`, `.idea/`, `*.swp`
- Env: `.env`, `.env.*`, `!.env.example`
- Build: `dist/`, `build/`, `out/`, `*.log`

#### .editorconfig
```ini
root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.py]
indent_size = 4

[*.md]
trim_trailing_whitespace = false

[Makefile]
indent_style = tab
```

### 3c — AI Context Files

These four files form the AI/agent context layer of the project.

#### CLAUDE.md and AGENTS.md (mirrors — always identical)

Both files must have **identical content**. Keep them in sync — they serve different agents
(some read `CLAUDE.md`, others prefer `AGENTS.md`).

```markdown
# AI Agent Context — <project-name>

This file provides context for AI assistants and coding agents working in this repository.
It is mirrored as both `CLAUDE.md` and `AGENTS.md` — keep them in sync.

## Project Overview

<description>

**Stack:** <stack>

## Key Files

| File | Purpose |
|------|---------|
| `CODEBASE.md` | Architecture, folder map, core concepts, key types |
| `llm-guidelines.md` | Behavioral guidelines for LLMs (when to ask, how to scope changes, avoid over-engineering) |
| `CONVENTIONS.md` | Code style, naming, commits, PR process |

## Quick Reference

- Read `CODEBASE.md` before making structural changes
- Follow the conventions in `llm-guidelines.md`
- When in doubt, ask before refactoring

## Notes

_Add project-specific conventions, gotchas, or constraints here._
```

#### llm-guidelines.md

This file contains **behavioral** guidelines for LLMs — how to think, when to ask, how to scope
changes. It is not a style guide or conventions doc (put those in CODEBASE.md).

**Important:** Before writing this file, check if the karpathy-guidelines skill is available:

```bash
find /mnt/skills -name "*karpathy*" 2>/dev/null
```

- **If found**: Read that skill's content and embed it as the body of `llm-guidelines.md`
- **If not found**: Use the verbatim content from `references/llm-guidelines-default.md` — do not
  summarize or rewrite it, copy it as-is

Wrap the content with this header:

```markdown
# LLM Guidelines — <project-name>

Behavioral guidelines for AI assistants working in this codebase.
Derived from Andrej Karpathy's observations on common LLM coding pitfalls.

---

<content from karpathy-guidelines skill OR references/llm-guidelines-default.md>
```

#### CODEBASE.md

```markdown
# Codebase Overview — <project-name>

> This file is a living document. Update it as the project evolves.

## Description

<description>

## Stack

<stack>

## Folder Map

```
<project-name>/
├── (fill in as project grows)
```

## Core Concepts

_TODO: Describe the main abstractions and mental model of this codebase._

## Key Types / Interfaces

_TODO: List the most important data structures, types, or interfaces._

## Patterns & Conventions

_TODO: Note recurring patterns, naming conventions, and architectural decisions._

## External Services & Dependencies

_TODO: List external APIs, databases, or services this project depends on._

## Known Gotchas

_TODO: Document any non-obvious behavior or tricky areas._
```

#### CONVENTIONS.md

A concise, scannable reference for how code is written in this project. Keep it short — if it
can't be skimmed in 2 minutes, it won't be read. Use the template from
`references/conventions-template.md`, filling in the stack-specific sections based on the user's
answers. Leave sections as `_TODO_` if not yet decided — better to be honest than prescriptive.

### 3d — CI/CD (optional)

If the user opted in, create `.github/workflows/ci.yml`.
See `references/ci-templates.md` for templates by stack and workflow type.

### 3e — Git setup

```bash
cd <project-dir>
git init
git add .
git commit -m "chore: initial project setup"
```

If a remote URL was provided:
```bash
git remote add origin <url>
git branch -M main
git push -u origin main
```

---

## Step 4 — Show Summary

After all files are created, print:
1. The full folder tree (`tree` command or manual listing)
2. A short text recap of what was created
3. Any next steps (e.g. "Fill in CODEBASE.md as you build", "Add your remote URL", etc.)

Also call `present_files` to surface the root folder to the user.

---

## Reference Files

- `references/folder-structures.md` — Stack-specific folder templates (Python, Node, Go, etc.). For Unity and C#/.NET, see their preset files
- `references/unity-preset.md` — Full Unity game dev preset: folder structure, official .gitignore, CODEBASE.md section
- `references/dotnet-preset.md` — C# / .NET preset: Console, Web API, and Class Library templates with .gitignore and .editorconfig additions
- `references/conventions-template.md` — CONVENTIONS.md template (fill in stack-specific sections at setup time)
- `references/llm-guidelines-default.md` — Default LLM guidelines (used when karpathy-guidelines is not installed)
- `references/ci-templates.md` — GitHub Actions workflow templates by stack/purpose

---
> Source: [Hissal/agent-plugins](https://github.com/Hissal/agent-plugins) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
