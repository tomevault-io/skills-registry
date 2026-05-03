---
name: dev-env-maintainer
description: Refactor and optimize development-environment repositories that contain dotfiles, setup scripts, and tool configs. Use when updating install flows, removing duplicated config logic, improving portability, or hardening shell scripts. Use when this capability is needed.
metadata:
  author: jam0818
---

# Dev Env Maintainer

## Workflow
1. Inspect repository structure, install scripts, and config links.
2. Identify duplicated setup logic and replace it with reusable manifests or helper functions.
3. Keep defaults safe and idempotent: rerunning setup should not break existing user files.
4. Prefer declarative mappings for file linking over hard-coded paths.
5. Update docs in the same change when setup behavior changes.

## Quality Bar
- Keep shell scripts POSIX-friendly unless Bash-specific features are required.
- Use guarded `source` and command checks to avoid startup failures.
- Back up existing files before replacing non-symlink config files.
- Minimize hard-coded usernames and absolute machine-specific paths.

## Validation
- Run syntax checks on changed shell scripts (`bash -n`).
- Run link/install scripts in dry-run mode when possible.
- Verify docs match actual file paths and commands.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jam0818) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
