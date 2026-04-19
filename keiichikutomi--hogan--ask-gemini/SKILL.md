---
name: ask-gemini
description: Gemini CLI にリサーチやコード分析を委任する Use when this capability is needed.
metadata:
  author: keiichikutomi
---

# /ask-gemini スキル

Gemini CLI にリサーチまたはコード分析を委任し、結果をユーザーに報告する。
Gemini は .gemini/GEMINI.md を自動読み込みするため、プロジェクトコンテキストを既に把握している。

## 1. タスク判定

$ARGUMENTS の内容から適切な呼び出し方を決定する:

- **リサーチ系**（ライブラリ調査、アルゴリズム等）→ プロンプトのみ
- **コード分析系**（ソース解析、依存関係等）→ `--include-directories src` を追加

**フォローアップの場合**: 同じトピックの既存ファイルがあれば、プロンプトに追加する:
- 「Read .claude/docs/research/gemini-xxx.md for previous discussion.」

## 2. Gemini 実行（バックグラウンド）

Bash ツールを **`run_in_background: true`** で実行する:

```bash
# リサーチの場合
gemini -p "Research: 質問内容"

# コード分析の場合（src と知識ベースを含める）
gemini -p "Analyze: 分析内容" --include-directories src .claude/docs
```

- `2>/dev/null` は付けない（stderr の進捗出力を残す）

## 2.5. 途中経過の確認

バックグラウンド実行中、**TaskOutput (block: false)** で定期的に進捗を確認する:

- 確認間隔: 約 15〜30 秒ごと
- 途中経過をユーザーにテキストで報告する
  - 例: 「Gemini 調査中です... ファイルを読み込んでいます」
  - 例: 「Gemini が応答を生成中です...」
- 出力が増えていなければ「まだ推論中です...」と報告する
- エラー出力が見えた場合は即座にユーザーに報告する

タスク完了後、**TaskOutput (block: true)** で最終結果を取得する。

## 3. ログ記録

実行結果を `.claude/logs/cli-tools.jsonl` に追記する:

```bash
echo '{"ts":"'$(date -Iseconds)'","agent":"gemini","prompt":"送信したプロンプト（要約）","summary":"回答の要約（1-2文）"}' >> .claude/logs/cli-tools.jsonl
```

## 4. 回答全文の保存（アペンドベース）

Gemini の回答（stdout 部分）を `.claude/docs/research/` に保存する。
同じトピックへのフォローアップは同一ファイルに追記し、擬似的なセッション継続を実現する。

### 新規トピックの場合

ファイルを新規作成する:

```markdown
# Gemini: トピック名

## Round 1 — YYYY-MM-DD
> Prompt: 送信したプロンプト

[Gemini の回答全文]
```

- ファイル名: `gemini-トピック.md`（例: `gemini-eigen-sparse-solver.md`）

### フォローアップの場合

既存ファイルの末尾に追記する:

```markdown

## Round N — YYYY-MM-DD
> Prompt: 送信したプロンプト

[Gemini の回答全文]
```

- Round 番号は既存ファイルの最後の Round 番号 + 1
- stderr（進捗出力）は含めず、最終回答のみ保存する
- 保存先パスをユーザーに報告する

## 5. 結果報告

Gemini の回答をユーザーに報告する。

- 回答の出典が Gemini であることを明記する
- 必要に応じて補足や修正を加える
- 保存先パスも報告する

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/keiichikutomi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
