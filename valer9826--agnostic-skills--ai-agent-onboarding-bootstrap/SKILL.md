---
name: ai-agent-onboarding-bootstrap
description: Creates and maintains environment-specific compatibility surfaces, a normalized MCP catalog, and a local bootstrap flow to prepare AI coding assistants (Cursor, Claude Code, Codex, Antigravity, or similar) for a repository.
metadata:
  author: valer9826
---

# AI Agent Onboarding Bootstrap

## Trigger
Use this skill when the request is to set up or bootstrap a developer AI environment for this repository, including prompts such as:
- `setup AI environment`
- `configure MCPs`
- `prepare Claude Code` / `prepare Cursor`
- `bootstrap agent config`
- `new developer AI onboarding`
- requests that clearly ask to align multiple AI tools with the repository agent layout

## Context/Knowledge
- **Canonical source of truth** for skills and (if present) workflows is defined in root `AGENTS.md`. Typical layouts:
  - Skills only under `skills/` (this catalog repository).
  - Skills under `.agents/skills/` and workflows under `.agents/workflows/` (integrated projects).
- **Compatibility surfaces** (examples): `.claude/**` for Claude Code, `.cursor/**` for Cursor — repo-tracked only when the team chooses; do not overwrite them unless the user asks for sync.
- **MCP servers** are optional and repository-specific. Maintain a short **normalized catalog** in docs or config (list server name, purpose, auth method). Example categories teams often use:
  - Source control (e.g. Git hosting API)
  - Issue tracker / docs (e.g. Atlassian)
  - Cloud provider or knowledge APIs
  - Domain-specific tools (design, analytics, etc.)
- **Secrets:** login-first or environment-variable-first; never commit tokens, passwords, or refresh tokens to tracked files.
- **Preflight:** verify required CLIs and logins before declaring bootstrap complete.

Replace placeholder lists in your project `AGENTS.md` with the MCP set and URLs your organization actually uses. Do not copy vendor- or project-specific URLs from other repositories.

## How/Execution
1. Read root `AGENTS.md` and identify the skills root, workflows root (if any), and any documented MCP expectations.
2. Refresh or generate only the compatibility surface that matches the **target environment** the user asked for (for example `.claude/**` or `.cursor/rules/**`).
3. If the repository documents a bootstrap script or checklist, follow it; otherwise produce a step list: install CLIs, configure MCP config files, log in to required services, open project.
4. Enforce blocking preflight behavior where possible:
   - verify required CLIs exist
   - verify required login or credential prerequisites
   - stop before partial setup if prerequisites are missing; list remediation steps
5. Keep machine-specific secrets and tokens out of git. Repo-tracked files should contain templates and placeholders only unless your security model explicitly allows otherwise.
6. Do not refresh one tool's compatibility layer while performing another tool's bootstrap unless the user asked for both.

## Output
- Updated target-environment compatibility surface when needed
- Reference to or copy of the normalized MCP catalog
- Inventory summary, dry-run summary, or bootstrap result
- Explicit report of missing prerequisites, applied changes, skipped MCPs, and reasons

## AI usage metrics (optional)
If the repository maintains a per-skill usage file, increment this skill's counter for the requesting developer after completing the workflow. Otherwise skip.

---
> Source: [valer9826/Agnostic-Skills](https://github.com/valer9826/Agnostic-Skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
