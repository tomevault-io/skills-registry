---
name: codex
description: Codex CLIを使ってコードレビュー・分析を自動実行するスキル。Claude Codeの相棒として複雑な問題解決やセカンドオピニオンの取得に活用できます。 Use when this capability is needed.
metadata:
  author: s4na
---

# Codex Skill

Codex CLIを使ってコードレビュー・分析を実行するスキルです。

## Codexとは

CodexはOpenAIのCLIツールで、コードレビュー・分析・コードベースへの質問を実行できます。
Claude Codeの「相棒」として、複雑な問題解決やセカンドオピニオンの取得に活用できます。

## 前提条件

このスキルを使用するには、Codex CLIがインストールされている必要があります。

インストールされていない場合は以下のコマンドでインストールしてください：

```bash
npm install -g @openai/codex
```

## 使い方

ユーザーが以下のようなリクエストをした場合にこのスキルを使用します：

- コードレビューを依頼された場合
- コードベースの分析を求められた場合
- セカンドオピニオンが必要な場合
- Codexを使った分析を明示的に依頼された場合

### 例

- 「このコードベースの構造を教えて」
- 「src/auth.ts のセキュリティレビューをして」
- 「認証フローの問題点を分析して」
- 「Codexで〜を調べて」

## 動作

ユーザーからのリクエストを受け取り、以下のコマンドを実行します：

```bash
codex exec --full-auto --sandbox read-only --cd "$PWD" "<リクエスト>"
```

### パラメータ説明

- `--full-auto`: 完全自動モードで実行（ユーザー確認なし）
- `--sandbox read-only`: 読み取り専用サンドボックスで実行（安全性確保）
- `--cd "$PWD"`: 現在のプロジェクトディレクトリを対象に実行

## 実行手順

1. ユーザーのリクエストを確認
2. Bashツールを使って以下のコマンドを実行：

```bash
codex exec --full-auto --sandbox read-only --cd "$(pwd)" "<リクエスト内容>"
```

3. 実行結果をユーザーに報告

## 注意事項

- Codex CLIがインストールされていない場合、エラーが発生します
- `--sandbox read-only` により、ファイルの書き込みは行われません
- OpenAI APIキーが環境変数に設定されている必要があります（`OPENAI_API_KEY`）
- 実行時間が長くなる場合があります（数分〜数十分）

## メリット（MCPではなくSkillとして実装した理由）

Skillとして実装することで以下のメリットがあります：

1. **進捗が見える**: リアルタイムで実行状況を確認できる
2. **中断判断が容易**: 出力を見ながら必要に応じて中断できる
3. **デバッグしやすい**: エラー出力が直接見える
4. **自動的に使用**: 関連するタスクで自動的に活用される

## 参考

- [Claude CodeとCodexの連携改善](https://zenn.dev/owayo/articles/63d325934ba0de)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/s4na) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
