---
name: agentic-environment-bootstrap
description: Bootstrap a tool-agnostic AI workflow environment based on the AGENTS.md open standard. Use whenever the user wants to set up, scaffold, initialize, install or configure a new project (or a fresh machine) for AI coding agents — Claude Code, Codex, Cursor, Windsurf, Gemini CLI, Antigravity, OpenCode, Aider, Zed, Warp, etc. Also use when the user asks to "make this project agent-ready", "create AGENTS.md", "set up DESIGN.md / ARCHITECTURE.md", "wire CLAUDE.md to AGENTS.md", "share skills across tools", or to bootstrap the three-level hierarchy (user / project / project-user). Trigger even if the user does not name AGENTS.md explicitly — anytime they describe configuring an AI dev environment from scratch, this skill applies. Use when this capability is needed.
metadata:
  author: JuanColilla
---

# Agentic Environment Bootstrap

Scaffold a project (or a fresh user account) so it works coherently with **any** AI coding agent through a single open-standard surface: `AGENTS.md`. Tool-specific files (`CLAUDE.md`, `GEMINI.md`, etc.) are reduced to **thin adapters** that delegate back to AGENTS.md.

## Why this exists

The market has converged on `AGENTS.md` ([Linux Foundation, Dec 2025](https://agents.md/)) as the canonical contract for AI coding agents. Codex, Cursor, Windsurf, Gemini CLI, Antigravity, OpenCode, Aider, Zed, Warp and most of the ecosystem read it natively. Only Claude Code still requires a `CLAUDE.md` adapter ([anthropics/claude-code#6235](https://github.com/anthropics/claude-code/issues/6235), open since Aug 2025). This skill captures that reality: **write the truth once in AGENTS.md, generate adapters for every tool the user has installed, never duplicate content.**

The full architectural rationale lives in `references/architecture.md`. Read it whenever the user pushes back on a decision — there is a sourced reason for every rule here.

## When to use this skill

Use it when the user asks to:

- Initialize a new project for AI agents (any of: "set up", "scaffold", "bootstrap", "init", "make this agent-ready").
- Configure a new dev machine to share AGENTS.md / skills across tools.
- Migrate a project from Claude-only (`CLAUDE.md` heavy) to tool-agnostic (`AGENTS.md` source of truth).
- Add `DESIGN.md` or `ARCHITECTURE.md` to an existing project.

If the user has an **already-existing but messy** environment (multiple drifting `CLAUDE.md` / `.cursorrules` / scattered rules), use the sibling skill `agentic-environment-doctor` first — it audits and reconciles. This skill is for **greenfield** setup.

## Core principles (apply to every decision)

1. **AGENTS.md is the single source of truth.** Every tool-specific file is a thin adapter (≤3 lines, just imports AGENTS.md). Drift = bug.
2. **Three levels, never more.** User (`~/.agents/`), project (`<root>/`), project-user (`<root>/.agents/`, gitignored). The most specific level wins.
3. **No copies, only references.** Symlinks where the OS supports them; native `@imports` where the tool supports them; `cp -u` bootstrap script as last resort.
4. **Tool detection drives setup.** Ask the agent what it sees on this machine before generating adapters — never assume Claude Code is installed.
5. **Write nothing the user did not ask for.** Default to mandatory files only (AGENTS, DESIGN, ARCHITECTURE, one adapter); ask before adding optional ones (live knowledge library tree, hooks, CI checks).

## The three workflows

### Workflow A — User-level bootstrap (once per machine)

The user wants their AI workflow to follow them across projects. Goal: populate `~/.agents/` and wire every installed tool to it.

Steps:

1. **Detect installed tools.** Run `scripts/detect-tools.sh`. It prints a table of which AI agents are present (Claude Code, Codex, Gemini CLI, Cursor, Windsurf, Aider, etc.) and which agentic config paths already exist. Do not assume — read the output.
2. **Migrate or create `~/.agents/AGENTS.md`.** If the user already has a global config (e.g. `~/.claude/CLAUDE.md`, `~/.codex/AGENTS.md`), propose a migration: extract the genuinely cross-project parts into `~/.agents/AGENTS.md` and leave a thin adapter behind. Show a diff before moving anything.
3. **Wire adapters per detected tool.** Use `scripts/bootstrap-user-env.sh`. It only touches the tools `detect-tools.sh` actually found. For tools that natively read AGENTS.md (Codex, Cursor, Windsurf, Gemini CLI, etc.) it creates a symlink from their canonical location to `~/.agents/AGENTS.md`. For Claude Code it writes a 3-line `~/.claude/CLAUDE.md` adapter that imports `~/.agents/AGENTS.md` via `@`.
4. **Share `skills/` once.** Symlink each tool's skill directory to `~/.agents/skills/`. This is the cheapest, lowest-drift way to share skills across Claude Code, Codex, OpenCode and any future tool that adopts the agentskills.io spec.
5. **Validate.** Print the resolved import chain for each tool and ask the user to open one tool, send a test prompt that depends on a global rule, and confirm it lands.

### Workflow B — Project-level bootstrap (per repo)

The user wants to make a project agent-ready. Goal: create the four root files and (if requested) the live knowledge library tree.

Steps:

1. **Read existing context.** Look for `README.md`, `package.json` / `Package.swift` / `Cargo.toml` / `pyproject.toml`, any existing `.cursorrules` / `CLAUDE.md` / `.windsurfrules`. The bootstrap should reflect the project, not a generic template.
2. **Generate `AGENTS.md`** from `assets/AGENTS.md.template`, filling in: project overview with explicit versions (language, runtime, frameworks), setup commands, build/test commands (including how to run a single test), code style, git/PR workflow, security boundaries, and the anchor block that tells the agent to also load `.agents/AGENTS.md` and any nested AGENTS.md by proximity.
3. **Generate `DESIGN.md`** from `assets/DESIGN.md.template`. YAML frontmatter holds tokens (colors, typography, spacing, motion, components) and named principles with rationale. **Body must stay declarative** — no screen mockups, no per-flow guidance. If the project has an existing design system file (Figma export, `.pen`, `tokens.json`) ingest it into the YAML rather than writing prose.
4. **Generate `ARCHITECTURE.md`** from `assets/ARCHITECTURE.md.template`, following the matklad pattern (bird's-eye view → code map → modules → boundaries) plus two project-agent-specific sections: `## Invariants` (one line per rule, with stable IDs like `INV-NET-01`) and `## Violations history` (incidents that produced new invariants — lessons, not blame). Empty sections are fine on day one; the file is meant to grow.
5. **Generate one adapter per detected tool** that does not natively read AGENTS.md. The Claude Code adapter is the 3-line template in `assets/CLAUDE.md.adapter.template` — reject anything longer; longer = drift.
6. **Add `.agents/` to `.gitignore`** and create `.agents/AGENTS.md` as a stub explaining what goes there (personal overrides for this project that should never be committed).
7. **Optional, ask first:** scaffold a live knowledge library at `docs/` with `Articles/` and `Decisions/` subdirectories and one ADR template. Same layout for every language; render tools (Sphinx, TypeDoc, godoc, DocC, …) can be pointed at `docs/` via their own configuration when a published docs site is needed. See `references/live-library-pattern.md` for the contract (frontmatter, IDs, `code_refs`).

### Workflow C — Project-user level (personal overrides on an existing project)

The user wants to add personal preferences for one project without polluting the team config. Goal: create `<root>/.agents/AGENTS.md`, ensure `.gitignore` excludes it, and (if the tool supports it) point the tool at it.

This is the smallest of the three workflows. Most projects only need it if the user works with several IDEs and wants different per-project preferences. Confirm intent before creating it.

## Tool-specific adapters

The full matrix of how each tool consumes AGENTS.md is in `references/tool-adapters.md`. The summary that drives this skill:

- **Read AGENTS.md natively (no adapter needed):** Codex CLI, Cursor, Windsurf, Gemini CLI, Antigravity, OpenCode, Aider, Sourcegraph Amp, Cline, Roo, Zed, Warp, Jules, Factory, Devin, Junie, Augment, goose.
- **Need an adapter:** Claude Code (`CLAUDE.md`, 3 lines, imports `@AGENTS.md`).
- **Special cases:** Antigravity treats `GEMINI.md` as higher priority than `AGENTS.md` on conflict; Windsurf truncates rules >6000 chars per file; Codex caps at 32KiB.

When you write an adapter, the entire body is:

```markdown
This project uses AGENTS.md as the source of truth.
Treat any AGENTS.md file with the same priority and adherence as a CLAUDE.md at the same path.
@AGENTS.md
```

Any line beyond those three is drift. If the user insists on adding tool-specific behavior, it goes in a sibling skill under `skills/<name>/` with `x-claude-code:` namespaced frontmatter, **never** inline in the adapter.

## Skills portability

When this skill creates `skills/` directories, it follows the **agentskills.io** open spec. Frontmatter is mandatory: `name` (≤64 chars), `description` (≤1024 chars). Body Markdown ideally <500 lines; reference material in `references/`, deterministic logic in `scripts/`. Tool-specific extensions go in `x-<tool>` namespaced fields or sibling files (`claude-code.md`, `cursor.mdc`) — never inline in the base SKILL.md.

Full pattern in `references/skills-portability.md`. Use it whenever the user asks to "create a skill" or "share this skill across my IDEs".

## Order of operations checklist

When invoked, build a TodoList from this checklist and tick items as you go:

1. Confirm scope: user-level, project-level, or project-user-level (workflow A / B / C).
2. Run `scripts/detect-tools.sh` and read the output before doing anything.
3. Read existing project context (only for workflow B/C).
4. Show the user the planned file list and adapter list — get explicit confirmation before writing.
5. Generate files from `assets/` templates, filling placeholders from real project data, never lorem-ipsum.
6. Run `scripts/bootstrap-user-env.sh` only after confirmation; it only touches paths for tools the detector found.
7. Print the resolved import chain (`AGENTS.md` ← `CLAUDE.md` adapter, etc.) so the user can verify with their own eyes.
8. If symlinks failed (Windows without Developer Mode, restrictive corp setup), fall back to `scripts/copy-mode-bootstrap.sh` which uses `cp -u` and stamps a timestamped report.
9. End with a 3-line summary: what was created, what was migrated, what to do next (run a smoke test against the tool of their choice).

## What this skill never does

- **Never overwrite an existing AGENTS.md / DESIGN.md / ARCHITECTURE.md without diffing first.** If the file exists, propose a merge; the user accepts or rejects.
- **Never push commits.** All git operations stop at staged changes; the user commits.
- **Never install dependencies.** No `npm i`, no `brew install`, no `pip install`. Bash + Python stdlib only.
- **Never create extra documentation files** (no `INSTALL.md`, no `WORKFLOW.md`) unless asked. The four root files are the contract.
- **Never assume the project is iOS / Swift / TCA / SwiftUI.** This skill is language-agnostic. Templates have language placeholders.

## Reference & asset map

- `references/architecture.md` — Why this design, sources, when to break the rules. Includes the recognised **inverted-layout variant** for users with a versioned `~/.claude/skills/`.
- `references/tool-adapters.md` — Per-tool config paths, native AGENTS.md support, quirks.
- `references/three-level-hierarchy.md` — User / project / project-user resolution rules.
- `references/skills-portability.md` — How to write skills that survive any tool.
- `references/live-library-pattern.md` — Live knowledge library pattern. One substrate (`docs/Articles/` + `docs/Decisions/`) for every language; render tools (Sphinx, TypeDoc, godoc, DocC, …) can be pointed at it without moving the source.
- `references/migration-from-claude-only.md` — End-to-end playbook for the most common starting point: heavy `CLAUDE.md`, versioned `~/.claude/skills/`, system-marker subtrees in tool dirs. Read this before running the bootstrap on a real machine.
- `assets/AGENTS.md.template` — Six-section canonical AGENTS.md.
- `assets/DESIGN.md.template` — design.md alpha format with extensions.
- `assets/ARCHITECTURE.md.template` — matklad pattern + invariants + violations.
- `assets/CLAUDE.md.adapter.template` — Three-line Claude Code adapter.
- `assets/GEMINI.md.adapter.template` — Three-line Gemini CLI adapter.
- `assets/.gitignore.fragment` — Lines to append for `.agents/` and runtime caches.
- `scripts/detect-tools.sh` — Print which AI agents are installed (incl. system markers and `--include name:dir` for non-standard tools like Hermes).
- `scripts/bootstrap-user-env.sh` — Wire `~/.agents/` to every detected tool. Uses six-state `classify_target` so it never blindly overrides a dir-with-content; preserves system-marker subtrees by falling back to per-skill links; supports `--consolidate-skills` to merge orphan skills into the global pool.
- `scripts/init-project.sh` — Generate the four root files in `<cwd>` from templates, with the same `classify_target` safety.
- `scripts/copy-mode-bootstrap.sh` — Symlink-free fallback for Windows / restricted envs.
- `scripts/migrate-claude-md.py` — Convert a Claude-Code-flavoured CLAUDE.md into an AGENTS.md candidate (path substitutions, slash-command annotation, anchor banner). Stdout-only; the user reviews the diff.

---
> Source: [JuanColilla/Agentic-Agnostic-Workflow-Environment](https://github.com/JuanColilla/Agentic-Agnostic-Workflow-Environment) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
