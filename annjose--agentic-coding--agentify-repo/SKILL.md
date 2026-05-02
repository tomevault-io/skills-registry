---
name: agentify-repo
description: Prepare any repository so coding agents can work effectively by generating and maintaining agent-facing docs from the repo’s real files and structure. Use when asked to agentify a repo, bootstrap AGENTS docs, set up Claude Code/Codex/Gemini/OpenCode instructions, or refresh runbooks and repo context after changes. Use when this capability is needed.
metadata:
  author: annjose
---

# Agentify Repo

Inspect the repository first, then generate only justified artifacts.

## Operating Rules

- Infer from repository evidence before writing docs.
- Cite concrete source files for every major statement.
- Prefer updating existing docs over creating duplicates.
- Keep docs minimal, actionable, and command-focused.
- Mark uncertain items explicitly as assumptions.
- Never invent deploy/test/build steps.

## Output Contract

Produce two passes.

1. Discovery + proposal pass
- Summarize detected stack, runtime, package managers, test tools, CI, deploy targets, and major subsystems.
- Propose artifact actions as `create`, `update`, or `skip`.
- Include an evidence table with `artifact | action | evidence paths | reason`.
- Ask for confirmation only if major ambiguity affects output.

2. Generation pass
- Create or update only approved artifacts.
- Keep wording repository-specific, not boilerplate.
- Reuse existing conventions and terminology.

## Core Artifacts

Default to these when absent and justified:

- `AGENTS.md` (source-of-truth contract and workflow for any agent)
- `docs/repo-structure.md` (architecture and directory map)
- `docs/runbook.md` (build/test/dev/release operations)
- `docs/decision-log.md` (key decisions and tradeoffs)
- `plans/README.md` (planning workflow, if planning is used)

## Conditional Artifacts

Add only when repo signals support them:

- Agent-specific files (for example `CLAUDE.md`, `CODEX.md`, `GEMINI.md`, `OPENCODE.md`) only when agent-specific deltas exist.
- `docs/deploy.md` when deployment/IaC/hosting config exists.
- `docs/api-contracts.md` when API schemas/routes/SDK contracts exist.
- `docs/data-model.md` when database schemas/migrations/models exist.
- `docs/content-style-guide.md` when content-heavy publishing docs are needed.
- `docs/taxonomy-conventions.md` when tagging/categorization systems exist.
- `scripts/check-content.sh` or similar checks when rules are automatable.

## Discovery Checklist

Collect evidence from:

- Root docs: `README*`, contribution guides, existing agent docs.
- Build/runtime files: `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, etc.
- Tooling: linters, formatters, test configs, task runners, hooks.
- CI/CD: `.github/workflows`, other pipeline configs.
- Infra/deploy: Docker, Terraform, Helm, cloud configs.
- App structure: entrypoints, modules, services, apps/packages directories.
- Data layer: migrations, schema files, ORM config.
- API layer: OpenAPI/GraphQL/proto/routes.
- Scripts: existing operational commands.

## AGENTS.md Minimum Structure

Include:

- Repo purpose and scope.
- Canonical commands for setup, dev, test, lint, build, release.
- Working agreements for all agents (Claude Code, Codex, Gemini, OpenCode).
- Code-change rules (small diffs, test expectations, no unrelated rewrites).
- Documentation update policy (which docs must be updated with code changes).
- Decision log policy and planning folder usage.
- Fast path checklist for common tasks.

## Per-Agent File Policy

For any agent-specific file (for example `CLAUDE.md`, `CODEX.md`, `GEMINI.md`, `OPENCODE.md`):

- Keep shared policy in `AGENTS.md`.
- Include only tool- or workflow-specific deltas.
- Avoid conflicting instructions across files.
- Link back to `AGENTS.md` as source of truth.

## Quality Gates

Before finalizing:

- Verify every command against repo tooling.
- Ensure path references exist.
- Remove duplicated guidance already present elsewhere.
- Keep each document concise and scannable.
- Ensure `docs/repo-structure.md` reflects current directories/services.
- List unresolved assumptions in a short section.

## Non-Goals

- Do not rewrite product/user-facing docs unless explicitly requested.
- Do not infer architecture beyond repository evidence.

## Maintenance Mode

When asked to refresh existing artifacts:

- Diff docs against current repo state.
- Update only stale sections.
- Preserve user-authored decisions unless explicitly superseded.
- Append to `docs/decision-log.md` instead of rewriting history.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/annjose) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
