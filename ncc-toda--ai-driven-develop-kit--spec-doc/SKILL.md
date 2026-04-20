---
name: spec-doc
description: 開発用ドキュメント（UI/UX 方針、画面設計書、機能設計書）の作成・更新・レビューを支援するスキル。新規画面や機能の設計ドキュメント作成時、既存ドキュメントの更新時、ドキュメントの整合性レビュー時に使用する。「画面設計書を作成して」「機能設計書をレビューして」「UI/UX 方針を更新して」などのリクエストで発動。 Use when this capability is needed.
metadata:
  author: ncc-toda
---

# 開発ドキュメント管理スキル

開発時に作成・更新すべきドキュメントを整理し、テンプレートに沿った記載と整合性確保を支援する。

## クイックスタート

### どのドキュメントを作成すべきか

| ドキュメント   | 目的                                               | いつ作成するか             |
| -------------- | -------------------------------------------------- | -------------------------- |
| **UI/UX 方針** | プロダクト全体の UX 原則・トンマナ・状態表示を統一 | プロジェクト開始時（1 回） |
| **画面設計書** | 画面単位の UI/挙動/状態を仕様化                    | 画面追加・変更時           |
| **機能設計書** | 機能単位の振る舞い・データ設計・非機能要件を定義   | 機能追加・変更時           |

### ワークフロー

```text
新規作成:
  1. テンプレートを読み込む（references/ 参照）
  2. プレースホルダー {xxx} を埋める
  3. 整合性チェック（下記参照）

更新:
  1. 変更箇所を特定
  2. 関連ドキュメントへの影響を確認
  3. 整合性チェック

レビュー:
  1. レビューチェックリストに沿って確認
  2. ドキュメント間の整合性を検証
```

## テンプレート選択

### UI/UX 方針を作成する場合

[ui-ux-concepts-template.md](references/ui-ux-concepts-template.md) を使用

```yaml
含まれるセクション:
  - ターゲットユーザー/ペルソナ
  - UX 原則（3〜5 原則）
  - トンマナ/UI 方針
  - 情報設計/ナビゲーション
  - 状態の見せ方（loading/empty/error/success）
  - フィードバック/アクセシビリティ
  - 計測/品質基準
```

使用例: [ui-ux-concepts-example.md](references/ui-ux-concepts-example.md)

### 画面設計書を作成する場合

[screen-design-template.md](references/screen-design-template.md) を使用

```yaml
含まれるセクション:
  - 概要（目的/想定ユーザー/表示条件）
  - 画面フロー/ナビゲーション
  - レイアウト/UI 要素
  - データ契約（queries/mutations）
  - 状態設計（initial/loading/empty/error/success）
  - ユーザー操作とイベント
  - エラーハンドリング
  - アクセシビリティ/テスト観点
```

使用例: [screen-design-example.md](references/screen-design-example.md)

### 機能設計書を作成する場合

[function-design-template.md](references/function-design-template.md) を使用

```yaml
含まれるセクション:
  - 概要（目的/対象画面/前提条件/依存）
  - スコープ（in/out/assumptions/constraints）
  - ドメイン定義（用語/エンティティ）
  - ユースケース（mainFlow/alternativeFlows/errorFlows）
  - 画面との対応
  - データ契約
  - 状態設計/状態遷移
  - エラー・例外設計
  - 非機能要件（品質）
  - テスト設計/受け入れ基準
```

使用例: [function-design-example.md](references/function-design-example.md)

## ドキュメント間の整合ルール

```yaml
整合チェック:
  - UI/UX 方針の原則 → 画面設計書・機能設計書に反映されているか
  - 画面設計書の interactionId ↔ 機能設計書の画面対応
  - 画面設計書の dataContract ↔ 機能設計書の dataContract（同一名称/型）
  - 文言・用語 → UI/UX 方針の用語ルールと一致
  - 状態名（loading/empty/error/success）→ 全ドキュメントで統一
```

## レビューチェックリスト

### 記載品質

- [ ] 仕様は「何が起きるか/いつ起きるか」が明確
- [ ] 外部依存（API/権限）が冒頭に記載されている
- [ ] アクセシビリティ要件が省略されていない
- [ ] 状態名が統一されている

### 整合性

- [ ] 画面設計書の interaction ↔ 機能設計書の画面対応が一致
- [ ] dataContract の名称・型が一致
- [ ] 用語が UI/UX 方針と整合

### UX 品質（UI/UX 方針より）

- [ ] loading/empty/error/success が破綻していない
- [ ] 二重送信が起きない設計
- [ ] 戻る挙動が OS 標準に沿う
- [ ] タップ領域が十分（44dp 以上）

## 参考資料

- [開発ドキュメントガイド（全体像）](references/guide.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ncc-toda) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
