---
name: eval-harness
description: 評価駆動開発（EDD）原則を実装する Claude Code セッション用の正式な評価フレームワーク Use when this capability is needed.
metadata:
  author: rx-k8
---

# Eval Harness スキル

評価駆動開発（EDD）原則を実装する、Claude Code セッション用の正式な評価フレームワーク。

## 哲学

評価駆動開発は、評価を「AI 開発の単体テスト」として扱います:
- 実装前に期待される動作を定義
- 開発中に評価を継続的に実行
- 各変更でのリグレッションを追跡
- 信頼性測定に pass@k メトリクスを使用

## 評価タイプ

### 機能評価
Claude が以前できなかったことができるかテスト:
```markdown
[CAPABILITY EVAL: feature-name]
タスク: Claude が達成すべきことの説明
成功基準:
  - [ ] 基準 1
  - [ ] 基準 2
  - [ ] 基準 3
期待される出力: 期待される結果の説明
```

### リグレッション評価
変更が既存の機能を壊さないことを保証:
```markdown
[REGRESSION EVAL: feature-name]
ベースライン: SHA またはチェックポイント名
テスト:
  - existing-test-1: PASS/FAIL
  - existing-test-2: PASS/FAIL
  - existing-test-3: PASS/FAIL
結果: X/Y が合格（以前は Y/Y）
```

## グレーダータイプ

### 1. コードベースのグレーダー
コードを使用した決定論的チェック:
```bash
# ファイルに期待されるパターンが含まれているかチェック
grep -q "def handle_auth" src/auth.py && echo "PASS" || echo "FAIL"

# テストが通るかチェック
uv run pytest tests/test_auth.py && echo "PASS" || echo "FAIL"

# ビルドが成功するかチェック
uv run ruff check . && uv run mypy . && echo "PASS" || echo "FAIL"
```

### 2. モデルベースのグレーダー
Claude を使用してオープンエンドな出力を評価:
```markdown
[MODEL GRADER PROMPT]
以下のコード変更を評価:
1. 述べられた問題を解決しているか？
2. 適切に構造化されているか？
3. エッジケースが処理されているか？
4. エラーハンドリングは適切か？

スコア: 1-5（1=不良、5=優秀）
理由: [説明]
```

### 3. 人間グレーダー
手動レビュー用にフラグ:
```markdown
[HUMAN REVIEW REQUIRED]
変更: 何が変更されたかの説明
理由: なぜ人間のレビューが必要か
リスクレベル: LOW/MEDIUM/HIGH
```

## メトリクス

### pass@k
「k 回の試行で少なくとも 1 回成功」
- pass@1: 初回試行の成功率
- pass@3: 3 回以内の成功
- 典型的な目標: pass@3 > 90%

### pass^k
「すべての k 回の試行が成功」
- 信頼性のより高い基準
- pass^3: 3 回連続成功
- クリティカルパスに使用

## 評価ワークフロー

### 1. 定義（コーディング前）
```markdown
## EVAL DEFINITION: feature-xyz

### 機能評価
1. 新しいユーザーアカウントを作成できる
2. メール形式を検証できる
3. パスワードを安全にハッシュ化できる

### リグレッション評価
1. 既存のログインが引き続き動作
2. セッション管理が変更なし
3. ログアウトフローが無傷

### 成功メトリクス
- 機能評価の pass@3 > 90%
- リグレッション評価の pass^3 = 100%
```

### 2. 実装
定義された評価に合格するコードを書く。

### 3. 評価
```bash
# 機能評価を実行
[各機能評価を実行し、PASS/FAIL を記録]

# リグレッション評価を実行
uv run pytest tests/ -k "existing"

# レポートを生成
```

### 4. レポート
```markdown
EVAL REPORT: feature-xyz
========================

機能評価:
  create-user:     PASS (pass@1)
  validate-email:  PASS (pass@2)
  hash-password:   PASS (pass@1)
  全体:            3/3 合格

リグレッション評価:
  login-flow:      PASS
  session-mgmt:    PASS
  logout-flow:     PASS
  全体:            3/3 合格

メトリクス:
  pass@1: 67% (2/3)
  pass@3: 100% (3/3)

ステータス: レビュー準備完了
```

## 統合パターン

### 実装前
```
/eval define feature-name
```
`.claude/evals/feature-name.md` に評価定義ファイルを作成

### 実装中
```
/eval check feature-name
```
現在の評価を実行してステータスをレポート

### 実装後
```
/eval report feature-name
```
完全な評価レポートを生成

## 評価ストレージ

プロジェクトに評価を保存:
```
.claude/
  evals/
    feature-xyz.md      # 評価定義
    feature-xyz.log     # 評価実行履歴
    baseline.json       # リグレッションベースライン
```

## ベストプラクティス

1. **コーディング前に評価を定義** - 成功基準について明確に考えることを強制
2. **評価を頻繁に実行** - リグレッションを早期にキャッチ
3. **pass@k を経時的に追跡** - 信頼性のトレンドを監視
4. **可能な限りコードグレーダーを使用** - 決定論的 > 確率的
5. **セキュリティは人間がレビュー** - セキュリティチェックを完全に自動化しない
6. **評価を高速に保つ** - 遅い評価は実行されない
7. **評価をコードとバージョン管理** - 評価は一級成果物

## 例: 認証の追加

```markdown
## EVAL: add-authentication

### フェーズ 1: 定義（10 分）
機能評価:
- [ ] ユーザーがメール/パスワードで登録できる
- [ ] ユーザーが有効な認証情報でログインできる
- [ ] 無効な認証情報が適切なエラーで拒否される
- [ ] セッションがページリロードを超えて持続する
- [ ] ログアウトがセッションをクリアする

リグレッション評価:
- [ ] パブリックルートが引き続きアクセス可能
- [ ] API レスポンスが変更なし
- [ ] データベーススキーマが互換性あり

### フェーズ 2: 実装（可変）
[コードを書く]

### フェーズ 3: 評価
実行: /eval check add-authentication

### フェーズ 4: レポート
EVAL REPORT: add-authentication
==============================
機能: 5/5 合格（pass@3: 100%）
リグレッション: 3/3 合格（pass^3: 100%）
ステータス: 出荷準備完了
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rx-k8) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
