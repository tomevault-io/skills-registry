---
name: git
description: Git workflow and branching best practices. Use when working with git commands, creating branches, or pushing changes. Use when this capability is needed.
metadata:
  author: bendrucker
---

# Git

* **Never** `git push` to the default branch (usually `main` or `master`) unless I explicitly instruct you to do so.
* **Always** use a topic branch with a few brief word hyphenated name

## Commit Messages

For multi-line commit messages:

- **Simple (subject + body):** Use multiple `-m` flags. Each `-m` creates a separate paragraph:
  ```bash
  git commit -m "Subject line" -m "Body paragraph here."
  ```
- **Complex:** Use a heredoc to pass the message:
  ```bash
  git commit -m "$(cat <<'EOF'
  Subject line

  Body paragraph here.
  EOF
  )"
  ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bendrucker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
