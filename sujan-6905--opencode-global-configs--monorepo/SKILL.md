---
name: monorepo
description: Monorepo skill for workspace structure, package coordination, and build orchestration with TurboRepo or equivalent tooling Use when this capability is needed.
metadata:
  author: sujan-6905
---

# Monorepo Management Skill

## Use This Skill For

- multi-package repository structure
- workspace wiring and shared package setup
- build orchestration and script coordination
- dependency scoping across apps and packages

## Do Not Use This Skill For

- single-package repos with no workspace coordination problem
- feature work that does not affect package boundaries or orchestration

## Default Workflow

1. Identify the active package manager and workspace layout.
2. Check the current root scripts and orchestration config.
3. Use official docs before altering workspace behavior.
4. Change the minimum set of manifests and config files needed.
5. Verify that root-level commands still make sense.

## Structure Guidance

- Keep the root as the source of orchestration.
- Keep app and package boundaries clear.
- Avoid hidden coupling between packages.
- Use shared packages only when reuse is real.

## Turbo Guidance

- Use TurboRepo when it is already the chosen orchestrator or the user wants it.
- Keep pipeline definitions explicit.
- Avoid adding complex workspace machinery for small repos.

## Environment Rule

- If package-level env variables must be documented, update `.env.example` files instead of `.env`.
- Assume documented keys also exist in the local `.env` files.

## Done Criteria

- workspace configuration is consistent
- package boundaries remain clear
- root commands and documentation still reflect reality

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sujan-6905) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
