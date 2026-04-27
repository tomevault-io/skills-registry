---
name: docs-consistency
description: Verify and fix documentation to match implementation. Use when updating docs, releasing versions, or when user mentions 'docs consistency', 'docs verify', 'ドキュメント更新', 'docsを直して'. Use when this capability is needed.
metadata:
  author: tettuan
---

# Docs Consistency

ドキュメントは実装を説明するものなので、設計意図 → 実装調査 → docs 差分 → ギャップ修正の順で整合性を取る。

## Phases

1. **Design Intent** — `docs/` の設計ドキュメントから What/Why/制約を抽出する
2. **Implementation Survey** — `mod.ts`, `src/core/`, `src/types/`, `src/validation/`, `src/replacers/` の公開 API・デフォルト値・エッジケースを確認する
3. **Diff** — 設計+実装 vs 現行 docs のギャップ表を作る
4. **Fix** — 実装向けドキュメントのみ修正する（設計ドキュメントは変更しない）。優先順: README.md → docs/user_guide.md → docs/api_reference.md
5. **Verify** — `grep "^export" mod.ts` で公開 export とドキュメントの一致を確認する

言語: `*.md` = English、`*.ja.md` = Japanese。`/update-docs` (作成・更新)、`/update-changelog` (CHANGELOG) と併用。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tettuan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
