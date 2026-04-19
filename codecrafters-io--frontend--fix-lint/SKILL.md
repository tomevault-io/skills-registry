---
name: fix-lint
description: Run the frontend linter, analyze failures, and fix them iteratively. Use when linting fails, lint errors appear, or the user asks to fix lint issues in the frontend repo. Use when this capability is needed.
metadata:
  author: codecrafters-io
---

# Fix Linter Failures

## Steps

1. Run `bun run lint:fix`.
2. Review the output for failures.
3. Fix one issue at a time.
4. Re-run `bun run lint:fix` to confirm all issues are fixed.
5. If failures remain, repeat steps 3 and 4.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codecrafters-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
