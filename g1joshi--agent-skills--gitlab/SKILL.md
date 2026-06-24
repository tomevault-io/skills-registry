---
name: gitlab
description: GitLab DevOps platform with CI/CD and security. Use for DevOps. Use when this capability is needed.
metadata:
  author: g1joshi
---

# GitLab (Tool)

GitLab's CLI (`glab`) allows interaction with the GitLab instance (Cloud or Self-Managed) directly from the terminal.

## When to Use

- **Self-Managed**: GitLab is king of on-prem.
- **CI Linting**: `glab ci lint` to check `.gitlab-ci.yml`.

## Core Concepts

### GitLab CLI (`glab`)

Open source CLI tool.
`glab mr create --fill`

### Snippets

GitLab's version of Gists.

## Best Practices (2025)

**Do**:

- **Use `glab` for MRs**: Create and merge MRs without leaving the terminal.
- **Check Pipeline Status**: `glab ci status --live`.

**Don't**:

- **Don't ignore the Web IDE**: GitLab's VS Code-based Web IDE is powerful for quick edits.

## References

- [GitLab CLI](https://gitlab.com/gitlab-org/cli)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/g1joshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
