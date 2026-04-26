---
name: applying-code-principles
description: > Use when this capability is needed.
metadata:
  author: thkt
---

# コード原則

優先順位、閾値、競合解決: `rules/PRINCIPLES.md` と
`rules/development/CODE_THRESHOLDS.md` を参照。

## クイックチェック

| 質問               | 原則                    |
| ------------------ | ----------------------- |
| シンプルな方法は？ | Occam's Razor           |
| 1分で理解できる？  | Miller's Law            |
| 重複していない？   | DRY                     |
| 今必要？           | YAGNI                   |
| CSSでできる？      | Progressive Enhancement |

## ルール

| 原則                    | ルール                                    |
| ----------------------- | ----------------------------------------- |
| DRY                     | 3回目の重複で抽象化（Rule of Three）      |
| SOLID                   | 2つ目の実装が現れた時のみインターフェース |
| YAGNI                   | 問題が今存在する場合のみ構築              |
| Readable                | 新しいチームメンバーが1分以内に理解できる |
| Progressive Enhancement | HTML → CSS → JS（より前のレイヤーを優先） |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thkt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
