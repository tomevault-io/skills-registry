---
name: gold-standard-project-bootstrap
description: Use when starting a new software project or hardening an existing repo with a senior-engineering development system. Creates or proposes repo-local AGENTS.md, CI quality gates, branch protection guidance, PR templates, review checklists, autonomy boundaries, and Codex workflow docs without adding project-specific assumptions.
metadata:
  author: ardibabakan
---

# Gold Standard Project Bootstrap

Use this skill to help a repository grow a senior-engineering development system that matches the actual project. Keep the work generic until the repository has been inspected.

## Operating Principles

- Inspect the repo first.
- Identify the package manager, scripts, framework, test tools, CI, runtime, deployment surface, ownership model, and risk areas.
- Propose a repo-specific setup before editing.
- Prefer Plan Mode first for new, unclear, risky, production-facing, or broad project setup work.
- Use Regular Mode only for approved implementation.
- Keep documentation small, practical, and project-scoped.
- Avoid copying rules from unrelated projects.
- Do not add project-specific assumptions without evidence from the repo.

## Discovery Checklist

Before creating files, inspect:

- Project structure and main entry points.
- `PACKAGE_MANAGER`, lockfiles, and install commands already implied by the repo.
- Existing scripts for test, lint, typecheck, format, build, and preview.
- Framework, runtime, language versions, and environment files.
- Existing CI, release, deployment, and hosting configuration.
- Test tools and the current test surface.
- Risk areas such as authentication, authorization, payments, migrations, secrets, data deletion, production jobs, and external services.
- Existing docs, issue templates, PR templates, ownership files, and agent instructions.

## Recommended Output

After discovery, propose a concise repo-local setup. Create or update files only after the user approves the implementation scope.

Common files to create when useful:

- `AGENTS.md`
- `.github/workflows/quality.yml`
- `.github/pull_request_template.md`
- `.github/ISSUE_TEMPLATE/checkpoint.md`
- `docs/BRANCH_PROTECTION_AND_CI.md`
- `docs/SENIOR_ENGINEERING_REVIEW_CHECKLIST.md`
- `docs/AUTONOMY_LEVELS.md`
- `docs/CODEX_AGENT_WORKFLOW.md`
- `.codex/config.toml`
- `.codex/agents/*.toml`

Only create project-scoped `.codex/config.toml` and `.codex/agents` when they add clear value for the repository.

## CI Quality Gates

- Use only the project's real scripts.
- Do not invent commands that are not present or clearly supported.
- Prefer the smallest useful gate first: install, lint, typecheck, test, build.
- If a script is missing, recommend adding it separately instead of hardcoding assumptions.
- Use placeholders in proposed templates until the actual command is confirmed, such as `TEST_COMMAND` and `BUILD_COMMAND`.

## Boundaries

Do not:

- Install packages unless the user explicitly approves.
- Add secrets, tokens, billing changes, production database changes, deployments, external connectors, MCP servers, unsupervised browser or computer use, automations, auto-merge, or auto-deploy.
- Copy project-specific rules from another repo.
- Create bloated documentation.
- Touch production systems or external services as part of bootstrap work.

## Final Report

Every implementation must end with:

- Files changed.
- Checks run and results.
- Risks or skipped items.
- Suggested next step.

---
> Source: [ardibabakan/gold-standard-codex-project-template](https://github.com/ardibabakan/gold-standard-codex-project-template) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
