---
name: config-git
description: Configures git setup for user identity and project conventions. This skill should be used when the user asks to "configure git", "setup git", "set commit scopes", or needs project-specific Git settings.
metadata:
  author: fradser
---

1. Verify `git config user.name` and `user.email`; prompt if missing
2. `git-agent init --scope --force`
3. Read scopes from `.git-agent/config.yml`, validate naming:
   - Single words: use as-is
   - Multi-word: abbreviate to first letters (e.g., `multi-word` -> `mw`)
4. Create `.claude/git.local.md` from `${CLAUDE_PLUGIN_ROOT}/examples/git.local.md` with validated scopes

CLI reference: `${CLAUDE_PLUGIN_ROOT}/references/cli.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fradser) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
