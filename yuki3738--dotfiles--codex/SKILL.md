---
name: codex
description: | Use when this capability is needed.
metadata:
  author: yuki3738
---

# Codex

Codex CLIを使用してコードレビュー・分析を実行するスキル。

## 実行コマンド

codex exec --full-auto --sandbox read-only --cd <project_directory> "<request>"

## パラメータ

| パラメータ | 説明 |
|-----------|------|
| `--full-auto` | 完全自動モードで実行 |
| `--sandbox read-only` | 読み取り専用サンドボックス（安全な分析用） |
| `--cd <dir>` | 対象プロジェクトのディレクトリ |
| `"<request>"` | 依頼内容（日本語可） |

## 使用例

### コードレビュー
codex exec --full-auto --sandbox read-only --cd /path/to/project "このプロジェクトのコードをレビューして、改善点を指摘してください"

### バグ調査
codex exec --full-auto --sandbox read-only --cd /path/to/project "認証処理でエラーが発生する原因を調査してください"

## 実行手順

1. ユーザーから依頼内容を受け取る
2. 対象プロジェクトのディレクトリを特定する
3. 上記コマンド形式でCodexを実行
4. 結果をユーザーに報告

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yuki3738) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
