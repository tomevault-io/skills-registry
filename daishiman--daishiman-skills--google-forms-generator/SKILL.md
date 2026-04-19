---
name: google-forms-generator
description: | Use when this capability is needed.
metadata:
  author: daishiman
---

# Google Forms Generator

Google Forms APIを使用してフォームを自動生成するClaude Code専用スキル。

## 概要

ユーザーとの対話形式で要件をヒアリングし、Markdown形式で構成を確認後、
Google Forms APIを使用してフォームを作成する。

## 対応機能

| カテゴリ | 機能 |
|---------|------|
| **質問タイプ** | 11種類（短文/長文/ラジオ/チェック/プルダウン/スケール/グリッド/日付/時刻/評価） |
| **フォーム設定** | クイズモード、メール収集、公開設定 |
| **高度な機能** | 条件分岐、セクション、画像/動画埋め込み |
| **連携** | Google Drive（フォルダ移動）、Google Sheets（回答取得） |

## 前提条件

1. `.env`ファイルにGoogle OAuth認証情報が設定済み
2. Node.js 18以上がインストール済み
3. `googleapis`パッケージがインストール済み

## ワークフロー

### メインフロー（新規作成）
```
Phase 1: ヒアリング（01-interviewer.md）
    ↓
Phase 2: 構成設計（02-designer.md）
    ↓
  [06-validator.md で検証]
    ↓
  [ユーザー承認待ち]
    ↓
Phase 3: API実行（03-executor.md）
    ↓
Phase 4: 結果報告（04-reporter.md）
```

### 代替フロー

| パス | 説明 | エージェント |
|------|------|-------------|
| **テンプレート開始** | 既存テンプレートから素早く作成 | 05 → 02 → 06 → 03 → 04 |
| **フォーム修正** | 既存フォームの質問追加/削除/変更 | 09 → 03 → 04 |
| **回答取得** | フォームの回答データを取得・エクスポート | 08 |
| **エラー復旧** | API実行エラーからのリカバリ | 07 → 該当エージェント |
| **認証設定** | OAuth認証のセットアップ/トラブルシュート | 10 |

### フロー図
```
┌─────────────────────────────────────────────────────────────────┐
│                        開始トリガー                              │
└─────────────────────────────────────────────────────────────────┘
         │                    │                    │
         ▼                    ▼                    ▼
┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│ 新規作成    │      │ テンプレート │      │ 既存修正    │
│ (01)       │      │ (05)        │      │ (09)        │
└─────────────┘      └─────────────┘      └─────────────┘
         │                    │                    │
         └────────┬───────────┘                    │
                  ▼                                │
         ┌─────────────┐                           │
         │ 構成設計    │                           │
         │ (02)       │                           │
         └─────────────┘                           │
                  │                                │
                  ▼                                │
         ┌─────────────┐                           │
         │ 検証        │                           │
         │ (06)       │                           │
         └─────────────┘                           │
                  │                                │
                  └────────────┬───────────────────┘
                               ▼
                      ┌─────────────┐
                      │ API実行     │←── エラー時 ──→ (07)
                      │ (03)       │
                      └─────────────┘
                               │
                               ▼
                      ┌─────────────┐
                      │ 結果報告    │
                      │ (04)       │
                      └─────────────┘

別パス:
  ・回答取得 (08) - 独立して実行可能
  ・認証設定 (10) - 認証エラー時または初回セットアップ時
```

---

# 実行手順

## Phase 1: ヒアリング開始

ユーザーから以下の情報を収集する：

1. **フォームの目的・用途**
   - アンケート/申込/クイズ/お問い合わせ等

2. **基本情報**
   - タイトル
   - 説明文
   - 保存先フォルダID（オプション）

3. **質問項目**（1問ずつ詳細にヒアリング）
   - 質問文
   - 質問タイプ（下記参照）
   - 選択肢（該当する場合）
   - 必須/任意
   - 詳細説明（オプション）

4. **設定オプション**
   - クイズモード（正解・配点・フィードバック）
   - メール収集タイプ
   - 条件分岐の必要性

📖 詳細: [agents/01-interviewer.md](agents/01-interviewer.md)

---

## Phase 2: 構成設計

収集した要件をもとに：

1. API形式にマッピング
2. セクション構成・条件分岐ロジック設計
3. **確認用Markdownを生成**
4. ユーザーに確認を求める

### 確認用Markdown出力形式

```markdown
# {{title}} - フォーム構成確認

## 基本情報
| 項目 | 値 |
|------|-----|
| タイトル | {{title}} |
| 説明 | {{description}} |
| クイズモード | {{isQuiz}} |
| メール収集 | {{emailCollectionType}} |

## 質問一覧
| # | 質問文 | タイプ | 必須 | 選択肢 |
|---|--------|--------|------|--------|
| 1 | ... | ... | ... | ... |

## この構成でフォームを作成しますか？
```

📖 詳細: [agents/02-designer.md](agents/02-designer.md)

---

## Phase 3: API実行

ユーザー承認後、以下の順序でAPIを実行：

```javascript
// 1. フォーム作成（タイトルのみ）
const form = await forms.forms.create({ info: { title } });

// 2. 質問・設定追加（batchUpdate）
await forms.forms.batchUpdate({
  formId,
  requests: [
    { updateFormInfo: { ... } },
    { updateSettings: { ... } },
    { createItem: { ... } },
    // ...
  ]
});

// 3. 公開設定（2026年3月以降必須）
await forms.forms.setPublishSettings({
  formId,
  publishSettings: { publishState: { isPublished: true, isAcceptingResponses: true } }
});

// 4. フォルダ移動（指定時）
await drive.files.update({ fileId: formId, addParents: folderId });
```

📖 詳細: [agents/03-executor.md](agents/03-executor.md)

---

## Phase 4: 結果報告 & ファイル保存

作成完了後、**結果を `05_Project/GoogleFrom/` に保存**し、以下の情報を報告：

### 出力ディレクトリ構造

```
05_Project/GoogleFrom/
└── {YYYYMMDD_HHMMSS}_{タイトル}/
    ├── 01-design.md   # 下書き・設計情報（ヒアリング内容）
    └── 02-result.md   # URL情報・スプレッドシート情報等
```

### 報告内容

```markdown
## フォーム作成完了

| 項目 | 値 |
|------|-----|
| タイトル | {{title}} |
| 回答用URL | {{responderUri}} |
| 編集用URL | {{editUri}} |
| フォームID | {{formId}} |
| 保存先 | {{folderName}} |

## 保存されたファイル
- 05_Project/GoogleFrom/{{timestamp}}_{{title}}/01-design.md
- 05_Project/GoogleFrom/{{timestamp}}_{{title}}/02-result.md

## 次のステップ
- 回答用URLを共有してフォーム利用を開始
- スプレッドシート連携はフォーム編集画面の「回答」タブから設定可能
```

📖 詳細: [agents/04-reporter.md](agents/04-reporter.md)

---

# 質問タイプリファレンス

| # | UIでの名称 | タイプ指定 | パラメータ |
|---|-----------|-----------|-----------|
| 1 | 記述式（短文） | `SHORT_TEXT` | - |
| 2 | 段落（長文） | `LONG_TEXT` | - |
| 3 | ラジオボタン | `RADIO` | options, shuffle |
| 4 | チェックボックス | `CHECKBOX` | options, shuffle |
| 5 | プルダウン | `DROP_DOWN` | options |
| 6 | 線形スケール | `SCALE` | low, high, lowLabel, highLabel |
| 7 | 選択式グリッド | `GRID_RADIO` | rows, columns |
| 8 | チェックボックスグリッド | `GRID_CHECKBOX` | rows, columns |
| 9 | 日付 | `DATE` | includeYear, includeTime |
| 10 | 時刻 | `TIME` | duration |
| 11 | 評価（星/ハート/👍） | `RATING` | ratingScaleLevel, iconType |

📖 詳細: [references/question-types.md](references/question-types.md)

---

# 設定オプションリファレンス

## メール収集タイプ

| 値 | 説明 |
|----|------|
| `DO_NOT_COLLECT` | 収集しない（匿名回答を許可） |
| `VERIFIED` | Googleアカウントから自動取得 |
| `RESPONDER_INPUT` | 回答者が入力 |

## 条件分岐アクション

| 値 | 動作 |
|----|------|
| `NEXT_SECTION` | 次のセクションへ |
| `RESTART_FORM` | フォーム先頭へ戻る |
| `SUBMIT_FORM` | 即座に送信 |
| `goToSectionId: "{id}"` | 特定セクションへジャンプ |

📖 詳細: [references/form-settings.md](references/form-settings.md)

---

# リソースマップ

## agents/

### コアエージェント（メインフロー）

| ファイル | ペルソナ | 読み込み条件 |
|----------|---------|-------------|
| [01-interviewer.md](agents/01-interviewer.md) | Don Norman | Phase 1: 新規フォームのヒアリング時 |
| [02-designer.md](agents/02-designer.md) | Clayton Christensen | Phase 2: 構成設計時 |
| [03-executor.md](agents/03-executor.md) | Linus Torvalds | Phase 3: API実行時 |
| [04-reporter.md](agents/04-reporter.md) | Peter Drucker | Phase 4: 結果報告・ファイル保存時 |

### 補助エージェント（代替フロー・エラー処理）

| ファイル | ペルソナ | 読み込み条件 |
|----------|---------|-------------|
| [05-template-selector.md](agents/05-template-selector.md) | Steve Krug | 「テンプレート」「簡単に」「素早く」と言われた時 |
| [06-validator.md](agents/06-validator.md) | Martin Fowler | Phase 2完了後、API実行前（複雑な設定時は必須） |
| [07-error-handler.md](agents/07-error-handler.md) | Gene Kim | API実行エラー（400/401/403/404/429/500）発生時 |
| [08-response-manager.md](agents/08-response-manager.md) | Edward Tufte | 「回答を取得」「結果を確認」「エクスポート」時 |
| [09-form-modifier.md](agents/09-form-modifier.md) | Kent Beck | 「フォームを修正」「質問を追加/削除」時 |
| [10-auth-helper.md](agents/10-auth-helper.md) | Bruce Schneier | 認証エラー時または「認証」「セットアップ」時 |

## references/

| ファイル | 内容 |
|----------|------|
| [question-types.md](references/question-types.md) | 全11種類の質問タイプJSON構造 |
| [api-endpoints.md](references/api-endpoints.md) | 全14APIメソッド一覧 |
| [form-settings.md](references/form-settings.md) | フォーム設定パラメータ |
| [quiz-grading.md](references/quiz-grading.md) | クイズ・採点機能 |
| [branching-logic.md](references/branching-logic.md) | 条件分岐・ナビゲーション |
| [drive-integration.md](references/drive-integration.md) | Drive API連携 |
| [sheets-integration.md](references/sheets-integration.md) | Sheets API連携 |
| [authentication.md](references/authentication.md) | OAuth 2.0認証手順 |
| [limitations.md](references/limitations.md) | API制限事項 |

## scripts/

| パス | 用途 |
|------|------|
| `scripts/auth/setup-oauth.js` | 初回OAuth認証セットアップ |
| `scripts/auth/get-auth-client.js` | 認証クライアント取得 |
| `scripts/auth/refresh-token.js` | アクセストークンリフレッシュ |
| `scripts/forms/create-form.js` | フォーム作成 |
| `scripts/forms/add-questions.js` | 質問追加 |
| `scripts/forms/publish-form.js` | 公開設定 |
| `scripts/forms/update-settings.js` | フォーム設定更新（クイズ、メール収集等） |
| `scripts/drive/move-to-folder.js` | フォルダ移動 |
| `scripts/drive/set-permissions.js` | 共有設定管理 |
| `scripts/sheets/get-responses.js` | 回答データ取得 |
| `scripts/sheets/export-to-sheet.js` | 回答のスプレッドシートエクスポート |
| `scripts/output/save-form-result.js` | **結果をMarkdownで保存** |
| `scripts/utils/retry-with-backoff.js` | 指数バックオフリトライ |
| `scripts/utils/build-request.js` | リクエストビルダー |
| `scripts/utils/validate-config.js` | 設定ファイル検証 |

## templates/

| パス | 用途 |
|------|------|
| `templates/form-patterns/survey.json` | 顧客満足度調査テンプレート |
| `templates/form-patterns/event-registration.json` | イベント申込テンプレート |
| `templates/form-patterns/contact.json` | お問い合わせフォームテンプレート |
| `templates/form-patterns/quiz.json` | クイズテンプレート |
| `templates/form-patterns/feedback.json` | フィードバック収集テンプレート |
| `templates/form-patterns/custom.json` | 白紙テンプレート |
| `templates/question-builders/text-question.json` | テキスト質問ビルダー（SHORT_TEXT/LONG_TEXT） |
| `templates/question-builders/choice-question.json` | 選択式質問ビルダー |
| `templates/question-builders/scale-rating.json` | スケール・評価質問ビルダー（SCALE/RATING） |
| `templates/question-builders/rating-question.json` | 評価質問ビルダー（RATING専用） |
| `templates/question-builders/grid-question.json` | グリッド質問ビルダー |
| `templates/question-builders/date-time-question.json` | 日付・時刻質問ビルダー |

## 設定ファイル

| ファイル | 用途 |
|----------|------|
| `.env.example` | OAuth認証情報テンプレート |

---

# 制限事項

| 機能 | ステータス | 代替手段 |
|------|-----------|---------|
| ファイルアップロード質問 | ❌ API作成不可 | Web UIで手動追加 |
| 確認メッセージ設定 | ❌ REST API非対応 | Apps Script |
| 回答の検証（バリデーション） | ❌ 未サポート | Apps Script |
| スプレッドシート自動リンク | ❌ linkedSheetIdは読取専用 | UI手動設定 or 回答取得→書込 |

📖 詳細: [references/limitations.md](references/limitations.md)

---

# 変更履歴

| Version | Date | Changes |
|---------|------|---------|
| **1.3.0** | **2026-01-20** | **マルチエージェント拡張**: 6つの補助エージェント追加（05-template-selector, 06-validator, 07-error-handler, 08-response-manager, 09-form-modifier, 10-auth-helper）、ワークフロー図を拡張、代替フロー（テンプレート開始/フォーム修正/回答取得/エラー復旧/認証設定）をサポート |
| 1.2.2 | 2026-01-20 | **仕様準拠**: skill-design-spec.md準拠で3テンプレート追加（feedback.json, text-question.json, rating-question.json）|
| 1.2.1 | 2026-01-20 | **リファクタリング**: SKILL.md内テンプレートファイル名を実ファイル名と一致するよう修正（customer-survey→survey, quiz-template→quiz, rating-question→scale-rating）、choice-question/contactを追加 |
| 1.2.0 | 2026-01-20 | **仕様準拠完了**: 8ファイル追加（refresh-token, update-settings, set-permissions, export-to-sheet, validate-config, custom.json, date-time-question.json, .env.example） |
| 1.1.0 | 2026-01-20 | **結果保存機能追加**: `05_Project/GoogleFrom/`にdesign/result Markdownを自動保存 |
| 1.0.0 | 2026-01-20 | 初版リリース |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daishiman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
