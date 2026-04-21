---
name: cmd-generator
description: > Use when this capability is needed.
metadata:
  author: t2hnd
---

# CLAUDE.md Generator

CLAUDE.md is a context file that Claude Code reads automatically at the start of every session.
It replaces the need for /init and ensures consistent, project-aware behavior.

## Core Principle

**Only include what Claude does NOT already know.** Claude is already an expert programmer.
CLAUDE.md should contain project-specific knowledge: your conventions, your architecture decisions,
your commands, your constraints. Not general programming tutorials.

## Generation Workflow

### 1. Gather Project Context

Ask the user these questions (adapt based on what's already known):

- What is the project? (brief purpose, 1-2 sentences)
- What tech stack? (language, framework, major libraries, versions if critical)
- What are the key commands? (dev, build, test, deploy, lint)
- Any specific coding conventions? (naming, file organization, import style)
- Any "never do this" rules? (common mistakes to avoid in this codebase)
- Is there an existing codebase to analyze?

If the user provides access to an existing repo, scan it to infer:

- Package manager and key dependencies (package.json, requirements.txt, etc.)
- Directory structure (top-level layout)
- Existing linting/formatting config (.eslintrc, .prettierrc, pyproject.toml, etc.)
- Test framework in use
- Build/run scripts

### 2. Compose CLAUDE.md

Structure the file with these sections. **Include only sections relevant to the project.**
Every section should be concise — prefer bullet points and one-liners over paragraphs.

```
# Project Overview
One or two sentences: what this project is and the core tech stack.

# Commands
List of essential commands the developer uses regularly.
Format: `command` — brief description

# Architecture / Structure
Key directories and their purposes.
Only top-level and important nested dirs — not an exhaustive tree.

# Coding Conventions
Project-specific rules only. Skip anything that's standard for the language/framework.
Examples: naming patterns, import conventions, component patterns, error handling approach.

# Key Decisions
Important architectural choices that affect how code should be written.
Examples: state management strategy, auth approach, API design pattern.

# Do NOT
Explicit prohibitions. Things Claude should never do in this project.
Examples: don't add deps without discussion, don't use X pattern, don't modify Y file directly.

# Testing
How to write and run tests. Test file naming, framework, fixture conventions.

# Additional Context (optional)
Links to external docs, API references, or anything else Claude should know.
```

### 3. Review Checklist

Before delivering, verify:

- [ ] No generic programming knowledge (Claude already knows it)
- [ ] No secrets, credentials, or environment-specific values
- [ ] No contradictory rules
- [ ] Commands are copy-pasteable and accurate
- [ ] Each line provides project-specific value
- [ ] File is under 200 lines (ideally under 100)
- [ ] Language matches user preference (Japanese or English)

## For Examples & Anti-patterns

See [references/examples.md](references/examples.md) for concrete good/bad examples.

## Tips

- If the user has a monorepo, suggest one root CLAUDE.md + per-package CLAUDE.md files
- For existing projects, offer to analyze the codebase first and draft based on findings
- Suggest the user iterate on CLAUDE.md over time as conventions evolve
- Remind users they can also create `~/.claude/CLAUDE.md` for personal global preferences
  (e.g., "respond in Japanese", "use concise commit messages")

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t2hnd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
