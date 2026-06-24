---
name: agentic-app-framework
description: Bootstrap and maintain a reusable PRD/TDD/ADR documentation system for software repositories. Use when Codex needs to initialize the agentic app framework in a repo, copy the framework docs and AGENTS.md into an existing project, replace template placeholders, or keep implementation documentation aligned with code changes by updating implementation-status, PRDs, TDDs, roadmap, and ADRs under the framework rules. Use when this capability is needed.
metadata:
  author: sspanogle
---

# Agentic App Framework

Use this skill to install or maintain the agentic documentation framework built around:

- `AGENTS.md`
- `docs/prd/`
- `docs/tdd/`
- `docs/ui-direction.md`
- `docs/design-system.md`
- `docs/implementation-status.md`
- `docs/roadmap.md`
- `docs/adr/`

## Bootstrapping Workflow

1. Determine whether the target repo is empty/new or already has product files.
2. If the repo is new and the user can start from the template repository directly, prefer the GitHub template flow instead of copying files manually.
3. If the repo already exists, run `scripts/bootstrap_framework.sh --target <repo-path>` from this skill.
4. After bootstrapping, replace top-level placeholders.
5. Ask the user for any still-missing product-specific information only if it cannot be inferred safely.

Default framework source repo:

- `/Users/sws/Development/hobby/agentic-app-framework`

If the framework repo lives elsewhere, pass `--source <path>` to the bootstrap script.

## Maintenance Workflow

Before meaningful implementation work:

1. Read `docs/implementation-status.md`.
2. Read the relevant PRDs and TDDs.
3. Read `docs/ui-direction.md` and `docs/design-system.md` for UI work.

After meaningful implementation work:

1. Update `docs/implementation-status.md` if implementation status materially moved.
2. Update the relevant PRD if product behavior changed.
3. Update the relevant TDD if implementation direction changed.
4. Update `docs/roadmap.md` if milestone sequencing or current phase changed.
5. Add an ADR only if a real architectural or structural decision was made.

Do not add ADRs for routine UI polish, minor refactors, or small test additions.

## Required Discipline

Use `references/maintenance-checklist.md` as the final pass whenever the task includes implementation changes.

When maintaining docs:

- keep status docs factual, not aspirational
- distinguish implemented behavior from planned behavior
- prefer updating existing docs over creating new process docs
- keep `AGENTS.md` as a router, not a second PRD

## Bundled Resources

- `scripts/bootstrap_framework.sh`: copy the framework into an existing repo safely
- `references/maintenance-checklist.md`: concise checklist for post-implementation doc maintenance

---
> Source: [sspanogle/agentic-app-framework](https://github.com/sspanogle/agentic-app-framework) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
