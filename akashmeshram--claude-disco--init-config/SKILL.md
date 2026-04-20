---
name: init-config
description: Initialize development configurations: CI/CD, linting, testing, Docker. Use for setting up project tooling. Use when this capability is needed.
metadata:
  author: akashmeshram
---

# Initialize Config

Set up development infrastructure and tooling configurations.

## Available Config Types

| Type | Description |
|------|-------------|
| `ci` | CI/CD pipeline (GitHub Actions, GitLab CI) |
| `lint` | Linting/formatting (ESLint, Prettier, Biome) |
| `test` | Testing setup (Vitest, Jest, Pytest) |
| `docker` | Containerization (Dockerfile, docker-compose) |
| `hooks` | Git hooks (Husky, lint-staged) |
| `all` | Full configuration suite |

## Quick Reference

| User Request | Action |
|--------------|--------|
| "Set up CI" | Create GitHub Actions workflow |
| "Add linting" | Configure ESLint + Prettier |
| "Initialize testing" | Set up test framework |
| "Add Docker support" | Create Dockerfile + compose |
| "Set up everything" | Full config initialization |

## Agent

Use `subagent_type: config-initializer` with a detailed prompt including:
- Which config type(s) to initialize
- Platform preferences (GitHub vs GitLab, etc.)
- Framework-specific requirements
- Any existing configs to preserve

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/akashmeshram) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
