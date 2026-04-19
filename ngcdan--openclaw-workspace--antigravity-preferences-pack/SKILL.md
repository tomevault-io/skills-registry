---
name: antigravity-preferences-pack
description: Preferences + coding-style rules pack for Google Antigravity IDE (agent-first IDE). Use when generating, refactoring, or reviewing code for Đàn in OF1 projects (Java/Spring + React/TypeScript) and when preparing DEVLOG.md entries or copy-pastable Pull Request titles/descriptions. Use when this capability is needed.
metadata:
  author: ngcdan
---

# Antigravity Preferences Pack (Đàn / OF1)

Apply Đàn’s preferences consistently when an Antigravity agent is asked to generate code, refactor, or prepare PR notes.

## Quick start (how to use)

When starting a coding task:
1. Read `references/core.md`.
2. If the task is Java/Spring, read `references/java-spring.md`.
3. If the task is React/TypeScript, read `references/react-typescript.md`.
4. If the user asks for PR text, read `references/pr-template.md`.
5. If the task is completed (or a chunk is completed), update/create `DEVLOG.md` using `references/devlog-template.md`.

## Working rules (always enforce)

- Follow existing OF1 patterns from the source code. Do not impose a new formatter or architecture style unless explicitly requested.
- Format code by hand to match:
  - 2-space indent, no tabs
  - <= 120 chars per line
  - line breaks are intentional (avoid aggressive hard-wrap)
- Comments are short and explain intent/why.
- No logging unless explicitly requested.
- Tests are on-request. If a change is risky, propose tests and ask.
- Ask before large/wide refactors or changes that are hard to roll back.

## Deliverables to produce (when applicable)

### A) DEVLOG entry
- Ensure `DEVLOG.md` exists at repo root.
- Append one entry per completed chunk of work.
- Do not include a "Files touched" section.

### B) Pull Request text
- Produce a PR title + PR description using the template in `references/pr-template.md`.
- Keep snippets minimal and only include the core logic.
- Keep config changes under **Configuration Required**.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ngcdan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
