---
name: slack
description: | Use when this capability is needed.
metadata:
  author: neversight
---

# Slack Web API Skill

Slack Web APIを使用してメッセージ・ユーザー操作を行う。

## 認証

Bearer tokenを`Authorization`ヘッダーで送信。

| 環境変数 | 用途 |
|----------|------|
| `CLAUDE_SLACK_TOKEN` | 全API操作（投稿、履歴取得、検索等） |

トークン取得・スコープ設定の詳細は [references/authentication.md](references/authentication.md) を参照。

## 使用方法

### メッセージ投稿

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/skills/slack/scripts/slack_api.py post_message --channel C1234567890 --text "Hello"
```

### チャンネル履歴取得

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/skills/slack/scripts/slack_api.py get_history --channel C1234567890 --limit 100
```

### スレッド取得

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/skills/slack/scripts/slack_api.py get_thread --channel C1234567890 --ts 1234567890.123456
```

### 投稿検索

**検索実行前に必ずクエリを検証すること:**

```bash
# 1. バリデーション
python3 ${CLAUDE_PLUGIN_ROOT}/skills/slack/scripts/validate_query.py "from:me keyword"

# 2. バリデーション成功後に検索実行
python3 ${CLAUDE_PLUGIN_ROOT}/skills/slack/scripts/slack_api.py search --query "from:me keyword"
```

### 指定日の自分の投稿取得

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/skills/slack/scripts/slack_api.py my_posts --date 2025-01-19
```

日付（YYYY-MM-DD形式）を指定して、その日の自分の投稿をすべて取得する。

### ユーザー情報取得

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/skills/slack/scripts/slack_api.py get_user --user U1234567890
```

## APIリファレンス

詳細なパラメータは [references/api_reference.md](references/api_reference.md) を参照。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
