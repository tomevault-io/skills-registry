---
name: open-issue
description: Create a GitHub issue. Use when the user wants to create an issue, open an issue, or report a bug/feature request. Use when this capability is needed.
metadata:
  author: sakuro
---

# Open Issue

Create a GitHub issue using `gh issue create`.

## Rules

- Use heredoc syntax (`$(cat <<'EOF' ... EOF)`) for `--body` to avoid shell escaping issues
- Structure the body with markdown headers (e.g. `## Summary`, `## Details`)
- Add `--label` when the issue type is clear (bug, enhancement, documentation, refactoring)
- If no existing label fits, suggest creating a new label before issue creation
- Never include AI attribution

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sakuro) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
