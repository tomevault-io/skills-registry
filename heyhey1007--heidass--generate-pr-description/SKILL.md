---
name: generate-pr-description
description: name: generate-pr-description Use when this capability is needed.
metadata:
  author: heyhey1007
---
---
name: generate-pr-description
description: |
  Git diffからPR説明文を生成する。"PR説明", "プルリクエスト説明", "PR description"などのリクエストで使用。
  プロジェクトのPRテンプレートがあればそのフォーマットに従う。
---

# PR説明文生成スキル

## 概要
Git diffを分析し、構造化されたPR説明文を生成します。

## テンプレート検索

以下の順序でPRテンプレートを検索し、見つかればその形式に従う：

1. `.github/PULL_REQUEST_TEMPLATE.md`
2. `.github/pull_request_template.md`
3. `PULL_REQUEST_TEMPLATE.md`
4. `docs/pull_request_template.md`

また、このスキルディレクトリ内の `templates/` にカスタムテンプレートを配置可能。

## 手順

1. プロジェクトのPRテンプレートを検索
2. `templates/` 内のテンプレートを確認
3. `git diff`または`git diff --staged`で変更内容を取得
4. テンプレート形式で説明文を生成

## デフォルト出力形式

```markdown
## 概要
<!-- 変更の概要を簡潔に記述 -->

## 変更内容
<!-- 主な変更点をリストで記述 -->
- 

## 変更理由
<!-- なぜこの変更が必要か -->

## 影響範囲
<!-- この変更が影響する範囲 -->
```

## 注意事項
- 変更の意図を明確に伝える
- 技術的な詳細と背景の両方を含める
- レビュアーが理解しやすい構成にする

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heyhey1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
