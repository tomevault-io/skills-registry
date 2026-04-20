---
name: review
description: Review current code changes for Google Style compliance, security issues, and test coverage. Uses Git MCP for diff analysis. Use when this capability is needed.
metadata:
  author: iwatake2222
---

`mcp__git` で変更を確認し、以下をチェック:
- Google Style (2-space indent, 80文字/行)
- Python: 型ヒント、docstring
- JavaScript: JSDoc、ES6+
- テストの存在
- 変更量300行以内
- OWASP Top 10 脆弱性

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iwatake2222) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
