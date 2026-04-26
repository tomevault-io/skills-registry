---
name: git-tagging-releases
description: Managing software versions and releases using Semantic Versioning (SemVer) and Git tags. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Git Tagging & Releases

Tags are pointers to specific points in your repository's history, typically used to mark release versions.

## Semantic Versioning (SemVer)
`MAJOR.MINOR.PATCH` (e.g., `2.3.1`)
- **MAJOR**: Breaking changes.
- **MINOR**: New features (backwards compatible).
- **PATCH**: Bug fixes (backwards compatible).

## Tagging Types
- **Lightweight Tags**: Just a pointer to a commit.
- **Annotated Tags**: Stored as full objects in the Git database. They include the tagger's name, email, and date. **Best practice for releases.**

## Automation
Use tools like `standard-version` or `semantic-release` to:
1.  Analyze commit messages (Conventional Commits).
2.  Bump the version number.
3.  Create a Git tag.
4.  Generate an automated `CHANGELOG.md`.

## Value
Provides clear milestones for deployment and allows users to depend on specific versions of your software.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
