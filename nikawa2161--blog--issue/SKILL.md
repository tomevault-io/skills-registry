---
name: issue
description: GitHub ISSUEを作成します。ユーザーがISSUEの作成を依頼したときに使用します。 Use when this capability is needed.
metadata:
  author: nikawa2161
---

# GitHub ISSUE作成

GitHub ISSUEを作成してください。

対象リポジトリ: https://github.com/nikawa2161/blog

## 実行手順

### 1. ISSUEテンプレートの確認

`.github/ISSUE_TEMPLATE/feature_request.md`を読み込んで、テンプレートの形式を確認してください。

### 2. ISSUE内容の確認

テンプレートに基づいて、ユーザーから以下の情報を収集してください：
- **タイトル**: ISSUEのタイトル（コミットメッセージ規約に従う）
- テンプレートで定義されている各項目の内容

### 3. ISSUEの作成

テンプレートから本文部分（YAMLフロントマターを除いた部分）を抽出し、ユーザーから収集した内容で埋めてISSUEを作成:

```bash
gh issue create --title "タイトル" --label "[テンプレートのYAMLフロントマターから取得]" --body "$(cat <<'EOF'
[テンプレートの本文をここに展開し、ユーザーの回答で埋める]
EOF
)"
```

**重要**: テンプレートファイルが更新された場合は、常に最新の内容を参照すること

### 4. ISSUE URLの表示

作成されたISSUE URLをユーザーに表示

## タイトルの命名規則

コミットメッセージ規約に従ってください：

- `feat`: 新機能の追加
- `fix`: バグ修正
- `docs`: ドキュメントのみの変更
- `style`: コードの意味に影響しない変更
- `refactor`: バグ修正や機能追加ではないコードの変更
- `perf`: パフォーマンス改善
- `test`: テストの追加・修正
- `chore`: ビルドプロセスやツールの変更

例: `feat: 新機能の追加`

## 注意事項

- タイトルは日本語で簡潔に記述
- **必ず** `.github/ISSUE_TEMPLATE/feature_request.md` を読み込んでテンプレート形式を確認すること
- テンプレートのYAMLフロントマター（`labels`など）の情報も `gh issue create` のオプションとして使用すること
- 必要に応じて追加のラベルやアサインを指定可能（`--label`、`--assignee`オプション）
- `gh issue create` のHEREDOCでは `'EOF'` とシングルクォートで囲む

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nikawa2161) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
