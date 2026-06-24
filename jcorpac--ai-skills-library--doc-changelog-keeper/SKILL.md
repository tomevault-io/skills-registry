---
name: doc-changelog-keeper
description: Maintaining human-readable, professional version histories using "Keep A Changelog" principles. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Changelog Keeper

A changelog is a curated, chronologically ordered list of notable changes for each version of a project.

## Principles
1. **Human Readable**: Written for humans, not for tools or git log consumers.
2. **Distinct Sections**: Group changes into categories (Added, Changed, Deprecated, Removed, Fixed, Security).
3. **Internal Links**: If possible, link to PRs or Issue numbers for further context.

## Sections
- **Added**: For new features.
- **Changed**: For changes in existing functionality.
- **Deprecated**: For soon-to-be-removed features.
- **Removed**: For now-removed features.
- **Fixed**: For any bug fixes.
- **Security**: In case of vulnerabilities.

## Best Practices
- **Don't use git logs**: Git logs are noisy and contain implementation details that users don't care about.
- **Update with every release**: Make it a part of your release checklist.
- **SemVer alignment**: Ensure your changelog reflects your semantic versioning.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
