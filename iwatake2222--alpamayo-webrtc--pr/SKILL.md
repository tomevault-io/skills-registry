---
name: pr
description: Create a pull request with change validation. Checks line count limit (max 300 lines) and ensures proper commit prefix. Uses GitHub MCP for PR operations. Use when this capability is needed.
metadata:
  author: iwatake2222
---

1. `git diff main --stat` で変更量確認（最大300行）
2. 300行超は分割を提案
3. PRタイトルにprefix: `feat:`, `fix:`, `docs:`, `refactor:`, `ci:`, `test:`, `chore:`
4. `mcp__github` または `gh pr create` でPR作成

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iwatake2222) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
