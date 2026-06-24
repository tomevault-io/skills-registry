---
name: gitlabglab
description: glab CLI basics and GitLab workflow overview. Use when working with GitLab repositories or adapting GitHub patterns to GitLab. Use when this capability is needed.
metadata:
  author: bendrucker
---
# glab

`glab` is the official GitLab CLI. This skill helps adapt GitHub (`gh`) patterns to GitLab.

## Terminology

- **Pull Request → Merge Request (MR)**: Use `glab mr` instead of `gh pr`
- **Repository → Project**: GitLab calls repositories "projects"
- **Actions → CI/CD**: Use `glab ci` for pipelines and jobs

## Key Rules

- Use `glab` for GitLab (never `gh`)
- Push branch before creating MR
- Use `--fill` to auto-populate from commits

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bendrucker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
