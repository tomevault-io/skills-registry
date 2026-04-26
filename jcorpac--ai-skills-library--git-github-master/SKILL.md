---
name: git-github-master
description: Professional strategies for publishing, collaborating, and managing repositories on GitHub. Use when this capability is needed.
metadata:
  author: jcorpac
---

# GitHub Mastery

GitHub is more than just a host; it's a collaboration platform. This skill covers the mechanics of taking a local repository live and working with others.

## Remote Configuration
- **Connect**: `git remote add origin <url>`
- **Verify**: `git remote -v`
- **Push**: `git push -u origin main`

## Authentication (SSH)
Using SSH keys is more secure and convenient than HTTPS with Personal Access Tokens.
1. **Generate**: `ssh-keygen -t ed25519 -C "your_email@example.com"`
2. **Add**: Add the `.pub` key to your GitHub account settings.
3. **Test**: `ssh -T git@github.com`

## GitHub CLI (`gh`)
The official CLI tool for managing GitHub from your terminal.
- **Create Repo**: `gh repo create <name> --public --source=. --remote=origin`
- **Manage PRs**: `gh pr create`, `gh pr checkout <id>`, `gh pr merge`

## Pull Request Workflow
1. **Branch**: Create a feature branch.
2. **Push**: Push to GitHub.
3. **Draft**: Create a Draft PR if the work is in progress.
4. **Review**: Address feedback and push updates.
5. **Merge**: Squashing or rebasing is preferred for a clean `main` history.

## Best Practices
- **README**: Every repo needs a professional README.
- **LICENSE**: Choose an appropriate license (MIT, Apache 2.0).
- **Security**: Never commit secrets or API keys (use `.gitignore`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
