---
name: doc-management
description: Guide users through structured workflows for creating new documents and updating existing ones. Use when user wants to write documentation, proposals, technical specs, decision docs, or similar structured content. Also use when user wants to update, revise, or improve existing documents. Trigger when user mentions writing docs, creating proposals, drafting specs, updating docs, revising content, improving documentation, or similar document management tasks. Use when this capability is needed.
metadata:
  author: wanx2land
---

# Doc Management

ドキュメントの新規作成と既存ドキュメントの更新を構造化されたワークフローで支援する。

## ワークフロー選択

ユーザーのリクエストに応じてワークフローを判定する。

**新規ドキュメントの作成？** → 「新規作成ワークフロー」に従う
**既存ドキュメントの更新？** → 「更新ワークフロー」に従う

判断が曖昧な場合はユーザーに確認する。

---

## 新規作成ワークフロー

3 ステージで構成される。

1. **コンテキスト収集** - ユーザーが関連コンテキストを提供し、Claude が明確化の質問をする
2. **構成と執筆** - セクションごとにブレインストーミング・キュレーション・執筆を反復する
3. **リーダーテスト** - 新しい Claude インスタンスでドキュメントをテストし、盲点を発見する

ユーザーにこの 3 ステージを提示し、ワークフローに従うかフリーフォームで進めるか確認する。
辞退した場合はフリーフォームで対応する。

各ステージの詳細手順は [references/create-workflow.md](references/create-workflow.md) を参照する。

---

## 更新ワークフロー

4 ステップで構成される。

1. **現状把握** - 既存ドキュメントを読み込み、構造・内容・品質を分析する
2. **変更方針の決定** - ユーザーの更新意図を理解し、変更範囲を明確にする
3. **段階的編集** - セクション単位で変更を適用し、ユーザーの確認を得る
4. **整合性チェック** - 全体を通読し、一貫性・矛盾・冗長を検証する

各ステップの詳細手順は [references/update-workflow.md](references/update-workflow.md) を参照する。

---

## 共通ガイドライン

### トーン

- 直接的かつ手続き的に進める
- 理由はユーザーの行動に影響する場合のみ簡潔に説明する
- アプローチを「売り込む」ことはしない

### 偏向管理

- ステージをスキップしたい場合: 確認してフリーフォームに切り替える
- ユーザーがフラストレーションを示した場合: 認めて、より速い進め方を提案する
- 常にユーザーにプロセスの調整権限を与える

### コンテキスト管理

- 不明な点が言及されたら即座に確認する
- ギャップを蓄積させない

### ファイル操作

- ドキュメントの作成・編集には Write/Edit ツールを使用する
- 全文再出力を避け、Edit で部分的に修正する
- ファイル名はドキュメントの種類に合わせて適切に命名する
  （例: `decision-doc.md`, `technical-spec.md`）

### 記述ルール

- 更新履歴（Changelog、変更ログ）はドキュメントに記載しない
- 絵文字の使用は避ける
- 1 ドキュメントが 1,000 文字を超える場合は複数ドキュメントへの分割を検討する
- ダイアグラムが必要な場合は Mermaid 記法を使用する

### 品質基準

- すべての文が意味を持つか確認する
- 汎用的なフィラーやスロップを排除する
- 冗長を除去し、各セクションが固有の価値を持つようにする

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wanx2land) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
