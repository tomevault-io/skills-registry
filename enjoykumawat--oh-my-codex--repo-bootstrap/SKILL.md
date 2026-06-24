---
name: repo-bootstrap
description: Bootstrap a new repository or retrofit an existing one with Codex-native scaffolding including AGENTS.md, .codex/config.toml, roles, skills, templates, scripts, docs, CI, and optional terminal observability. Use when starting a project, standardizing collaboration rules, or upgrading a repo for repeatable Codex workflows. Use when this capability is needed.
metadata:
  author: enjoykumawat
---

# Repo Bootstrap

## When to use

- Use when a repository needs Codex-native collaboration scaffolding from scratch.
- Use when an existing repo has ad hoc prompts or scattered docs and needs one coherent operating model.
- Use when you want safe defaults, verification gates, and a reusable skills catalog across projects.

## When not to use

- Skip for a narrow feature change that does not alter repo tooling or collaboration flow.
- Skip when the target repo already has a stronger, settled operating model you should preserve.
- Skip when you cannot validate the scaffold in the target environment.

## Workflow

1. Inspect the repository and decide whether you are bootstrapping from zero or retrofitting around existing conventions.
2. Install the minimum scaffold first: `AGENTS.md`, `.codex/config.toml`, role files, templates, scripts, and CI.
3. Add the skills catalog and only the highest-value skills for the target repo.
4. Decide whether the TUI and NDJSON telemetry path are worth enabling immediately.
5. Tune sandbox, approval, and verification defaults to the repo’s risk level.
6. Update docs so contributors understand how to use the new workflow.
7. Run verification and provide maintainers with the shortest adoption path.

## Output contract

- `installed-components`: scaffold pieces added or updated
- `customization-notes`: where the target repo should tune defaults
- `verification`: checks run and outcomes
- `adoption-guide`: the shortest path for maintainers to start using the scaffold

## Examples

- `Use $repo-bootstrap to retrofit this monorepo with Codex roles, skills, templates, and CI.`
- `Use $repo-bootstrap to scaffold a brand-new project with safe multi-agent defaults and the TUI companion.`

## Resources

- Read `references/bootstrap-sequence.md` for the recommended install order.
- Use `scripts/bootstrap-repo.sh` when copying the scaffold into another repository.

---
> Source: [enjoykumawat/oh-my-codex](https://github.com/enjoykumawat/oh-my-codex) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
