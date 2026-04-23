---
name: git-workflow
description: Git best practices and workflows including conventional commits, branching strategies, and collaboration patterns. Use when this capability is needed.
metadata:
  author: kprsnt2
---

# Git Workflow Best Practices

## Commit Messages (Conventional Commits)
- feat: New feature
- fix: Bug fix
- docs: Documentation
- style: Formatting
- refactor: Code restructuring
- test: Adding tests
- chore: Maintenance

Format: type(scope): description
Example: feat(auth): add password reset flow

## Branch Naming
- feature/TICKET-description
- fix/TICKET-description
- hotfix/description
- release/v1.0.0
- docs/description

## Workflow
- Keep commits atomic and focused
- Rebase feature branches on main
- Squash WIP commits before merge
- Never force push to shared branches
- Use pull request templates
- Require code reviews

## .gitignore
- Ignore build artifacts
- Ignore node_modules, venv, target
- Ignore local config (.env.local)
- Ignore IDE settings (.idea, .vscode)
- Ignore OS files (.DS_Store)

## Advanced
- Use git hooks with husky/pre-commit
- Sign commits with GPG
- Use git-crypt for secrets
- Bisect for finding bugs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kprsnt2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
