---
name: formatting-audits
description: > Use when this capability is needed.
metadata:
  author: thkt
---

# SOW/Spec採点 (100点満点)

## 採点

| カテゴリ   | スコア | フォーカス                   |
| ---------- | ------ | ---------------------------- |
| 正確性     | 0-25   | ✓/→/?マーカー、証拠          |
| 完全性     | 0-25   | 全セクション、テスト可能なAC |
| 関連性     | 0-25   | 目標 ↔ ソリューション、YAGNI |
| 実行可能性 | 0-25   | 具体的なステップ、実現可能性 |

## 減点

| 問題               | 減点 |
| ------------------ | ---- |
| 信頼度マーカーなし | -5   |
| 必須セクション欠如 | -10  |
| 曖昧なアクション   | -5   |
| YAGNI違反          | -5   |

AC-FRトレーサビリティとテストカバレッジ: `validating-specs` スキルに委譲。

## 閾値

| スコア | 判定        | アクション         |
| ------ | ----------- | ------------------ |
| 90-100 | PASS        | 次のフェーズへ進行 |
| 70-89  | CONDITIONAL | 修正後に再レビュー |
| 0-69   | FAIL        | 大幅な修正が必要   |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thkt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
