---
name: git-readonly-sync
description: Clone or pull public repositories in read-only workflows to gather reference material. Use when this capability is needed.
metadata:
  author: simonvanlaak
---

Use this skill to retrieve repository content for analysis without pushing changes.

Guidelines:
1. Only use read-only Git operations (`clone`, `fetch`, `pull`, `show`, `log`).
2. Do not commit, push, rebase, or rewrite history.
3. Prefer shallow clones for speed when full history is unnecessary.
4. Keep repository paths explicit in outputs.

Inputs (CLI args):
- `--repo`: repository URL (https or ssh).
- `--dest`: destination directory.
- `--branch`: branch name (default: main).
- `--depth`: shallow clone depth (default: 1).
- `--token-env`: env var name containing a read-only token (optional).
- `--token-username`: username for the token (optional, defaults to x-access-token).

Notes:
- For private repos, provide `--token-env` and ensure the token is supplied via 1Password.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/simonvanlaak) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
