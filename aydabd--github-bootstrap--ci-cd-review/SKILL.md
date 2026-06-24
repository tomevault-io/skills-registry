---
name: ci-cd-review
description: CI/CD review for workflows, artifact safety, caching, deployment gates, secrets, permissions, and reproducibility. Use when this capability is needed.
metadata:
  author: aydabd
---

## Purpose

CI/CD review for workflows, artifact safety, caching, deployment gates, secrets, permissions, and reproducibility.

## Review focus

- overbroad token permission
- unsafe shell
- missing gate
- cache poisoning
- non-reproducible build
- artifact leak

## Method

1. Inspect changed files and diff hunks relevant to this skill.
2. Use repository-native tools when available.
3. Prefer exact evidence from changed code.
4. Emit findings using the shared JSONL finding contract.
5. Avoid style-only comments unless they create maintainability or correctness risk.

## Tooling hints

- Use `grep` or editor search before opening files.
- Use `git`, `grep`, and `gh` CLI. These are universally available and sufficient for all review tasks.
- Do not depend on tools beyond `git`, `grep`, `cat`, `head`, `wc`, and `gh`.

---
> Source: [aydabd/github-bootstrap](https://github.com/aydabd/github-bootstrap) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
