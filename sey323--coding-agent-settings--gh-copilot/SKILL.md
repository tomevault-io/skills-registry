---
name: gh-copilot
description: | Use when this capability is needed.
metadata:
  author: sey323
---

# GH-Copilot

GitHub Copilot CLIを使用して実装・修正・テスト追加を実行するスキル。

実行に際して以下のスキルは呼び出さないでください

- codex
- gemini

## 実行コマンド

copilot \
 --add-dir <project_directory> \
 -p "<request>"

## プロンプトのルール

**重要**: copilotに渡すリクエストには、以下の指示を必ず含めること：

> 「確認や質問は不要です。具体的なコード変更案を提示し、修正後のコード全体や背景を出力してください。」

## パラメータ

| パラメータ        | 説明                           |
| ----------------- | ------------------------------ |
| `--add-dir <dir>` | 対象プロジェクトのディレクトリ |
| `-p "<request>"`  | 依頼内容（日本語可）           |

## 使用例

**注意**: 各例では末尾に「確認不要、具体案まで出力」の指示を含めている。

### バグ修正

copilot \
 --add-dir /path/to/project \
 -p "認証処理の競合状態を解消してください。確認や質問は不要です。具体的な修正案と必要な修正後コード全体を提示してください。"

### テスト追加

copilot \
 --add-dir /path/to/project \
 -p "エッジケースを含む網羅的なユニットテストを追加してください。確認や質問は不要です。具体的な改善案と修正後のテストファイル全体を提示してください。"

### パフォーマンス最適化

copilot \
 --add-dir /path/to/project \
 -p "メモリ効率を改善し、不要なアロケーションを削減してください。確認や質問は不要です。具体的な改善案と修正後コード全体を提示してください。"

## 実行手順

1. ユーザーから依頼内容を受け取る
2. 対象プロジェクトのディレクトリと変更対象ファイルを特定する
3. **プロンプトを作成する際、末尾に「確認や質問は不要です。具体的なコード変更案を提示し、必要に応じて修正後のコード全体を出力してください。」を必ず追加する**
4. 上記コマンド形式でCopilotを実行
5. 結果をユーザーに報告

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sey323) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
