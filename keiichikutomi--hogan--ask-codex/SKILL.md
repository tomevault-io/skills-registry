---
name: ask-codex
description: Codex CLI (gpt-5.3-codex) に質問を委任する Use when this capability is needed.
metadata:
  author: keiichikutomi
---

# /ask-codex スキル

Codex CLI に質問を委任し、結果をユーザーに報告する。
Codex は AGENTS.md を自動読み込みするため、プロジェクトコンテキストを既に把握している。

## 1. 質問の準備

$ARGUMENTS をそのまま日本語で使用する。

必要に応じて、関連する `.claude/docs/` のファイルパスをプロンプトに含める:
- 理論的な質問 → 「.claude/docs/theory/ を読んでコンテキストを把握せよ。」を追加
- 移行関連 → 「.claude/docs/migration/ を読んでコンテキストを把握せよ。」を追加

**フォローアップの場合**: 同じトピックの既存ファイルがあれば、プロンプトに追加する:
- 「.claude/docs/research/codex-xxx.md を読んで前回の議論を把握せよ。」

## 2. Codex 実行（バックグラウンド）

Bash ツールを **`run_in_background: true`** で実行する:

```bash
codex exec --model gpt-5.3-codex --sandbox read-only --full-auto "質問内容（日本語）"
```

- `2>/dev/null` は付けない（stderr の進捗出力を残す）
- Codex は read-only sandbox でプロジェクト全体を参照できる

## 2.5. 途中経過の確認

バックグラウンド実行中、**TaskOutput (block: false)** で定期的に進捗を確認する:

- 確認間隔: 約 15〜30 秒ごと
- 途中経過をユーザーにテキストで報告する
  - 例: 「Codex 推論中です... ファイルを読み込んでいます」
  - 例: 「Codex が応答を生成中です...」
- 出力が増えていなければ「まだ推論中です...」と報告する
- エラー出力が見えた場合は即座にユーザーに報告する

タスク完了後、**TaskOutput (block: true)** で最終結果を取得する。

## 3. ログ記録

実行結果を `.claude/logs/cli-tools.jsonl` に追記する:

```bash
echo '{"ts":"'$(date -Iseconds)'","agent":"codex","prompt":"送信したプロンプト（要約）","summary":"回答の要約（1-2文）"}' >> .claude/logs/cli-tools.jsonl
```

## 4. 回答全文の保存（アペンドベース）

Codex の回答（stdout 部分）を `.claude/docs/research/` に保存する。
同じトピックへのフォローアップは同一ファイルに追記し、擬似的なセッション継続を実現する。

### 新規トピックの場合

ファイルを新規作成する:

```markdown
# Codex: トピック名
refs: .claude/docs/theory/xxx.md, src/yyy.c

## Round 1 — YYYY-MM-DD
> Prompt: 送信したプロンプト

[Codex の回答全文]
```

- ファイル名: `codex-トピック.md`（例: `codex-explicit-timestep-review.md`）
- **`refs:` 行**: Codex に渡した参照ファイルのパスをカンマ区切りで記録する

### フォローアップの場合

1. 既存ファイルの `refs:` 行を読み取る
2. Codex プロンプトに調査ファイル自体 **と** `refs:` に記載された全参照ファイルの読み込み指示を含める
3. 回答を末尾に追記する:

```markdown

## Round N — YYYY-MM-DD
> Prompt: 送信したプロンプト

[Codex の回答全文]
```

- Round 番号は既存ファイルの最後の Round 番号 + 1
- 新たに参照したファイルがあれば `refs:` 行に追加する
- stderr（進捗出力）は含めず、最終回答のみ保存する
- 保存先パスをユーザーに報告する

## 5. 結果報告

Codex の回答をそのままユーザーに報告する。

- 回答の出典が Codex (gpt-5.3-codex) であることを明記する
- 必要に応じて補足や修正を加える
- 保存先パスも報告する

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/keiichikutomi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
