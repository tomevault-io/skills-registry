---
name: git-release
description: Automates the release process with changelog generation and semantic versioning.
metadata:
  author: sherloock
---

# Git Release Skill

Instructions for creating releases with proper versioning.

## Usage

When asked to create a release:

1. Analyze commit history since last tag.
2. Determine version bump (major/minor/patch) from conventional commits.
3. Generate or update changelog.
4. Create git tag and push (optionally create GitHub/GitLab release).

## Conventions

- Use conventional commits to drive version bumps.
- Changelog entries should be human-readable and grouped by type (feat, fix, etc.).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sherloock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
