---
name: devops
description: DevOps skill for build pipelines, containers, CI, deployment configuration, and operational safety Use when this capability is needed.
metadata:
  author: sujan-6905
---

# DevOps Skill

## Use This Skill For

- Dockerfiles and container builds
- CI or CD workflows
- deployment configuration and operational checks
- cloud runtime or hosting automation

## Do Not Use This Skill For

- application feature work unrelated to delivery or operations
- pure UI refinement or business-logic implementation

## Default Workflow

1. Read the existing deployment and build files first.
2. Check official docs for the platform or service being changed.
3. Confirm whether the task affects local dev, CI, staging, or production.
4. Make the narrowest change that satisfies the requirement.
5. Verify syntax and execution paths where possible.

## Container Standards

- Use multi-stage builds when they materially reduce image size or improve isolation.
- Pin images intentionally.
- Run as non-root where feasible.
- Include health checks when the runtime expects them.
- Keep `.dockerignore` aligned with the repo shape.

## CI and CD Standards

- Keep workflows deterministic.
- Avoid deprecated actions and platform features.
- Cache only what is safe and useful.
- Surface failures early in the pipeline.
- Keep secrets out of checked-in config.

## Environment Handling

- Never read or edit `.env` directly.
- Document deployment or runtime variables in `.env.example`.
- Assume the keys in `.env.example` also exist in the real `.env`.
- Reflect required environment changes in README or setup docs.

## Risk Control

- Treat deployment and deletion commands as dangerous.
- Do not hide destructive behavior in scripts.
- Prefer reversible changes and explicit rollback paths.

## Done Criteria

- pipeline or container config is internally consistent
- secrets are not exposed
- operational variables are documented in `.env.example` when needed
- verification steps are reported clearly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sujan-6905) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
