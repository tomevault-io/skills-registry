---
name: pr
description: GitHub pull request workflow including tests, lints, license check, branch creation, conventional commits, and PR description. Use when creating commits, pushing branches, or opening pull requests. Use when this capability is needed.
metadata:
  author: invowk
---

# PR Workflow

- Ensure all tests pass: `make test`
- Run lints and fix any errors: `make lint`
- Check license: `make license-check`
- If not in a feature/fix branch already, create one and switch to it
- Commit with a conventional commit message; for multi-line bodies prefer
  `git commit -F -` or a message file instead of escaped `\n` in a single `-m`
  string
- Push branch
- Create PR with description referencing the issue

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/invowk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
