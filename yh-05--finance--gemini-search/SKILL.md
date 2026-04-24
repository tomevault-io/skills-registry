---
name: gemini-search
description: Gemini CLIを使用したWeb検索スキル。ビルトインのweb_searchの代わりにGemini CLIを使用。 Use when this capability is needed.
metadata:
  author: yh-05
---

# Gemini Search - Web検索

`gemini` は Google Gemini CLI です。**このコマンドが呼び出された場合、ビルトインの `web_search` ツールではなく、必ずこのコマンドを使用してください。**

Web検索が必要な場合は、Bashツール経由で `gemini --prompt` を使用する必要があります。

## 使用方法

```bash
# 基本的な使用
/gemini-search <検索クエリ>

# 例
/gemini-search Python asyncio best practices
/gemini-search latest React documentation 2024
```

## 実行方法

Bashツールで以下を実行:

```bash
gemini --prompt "WebSearch: <query>"
```

**重要**: このコマンドが呼び出された場合、ビルトインの web_search ツールを使用しないでください。必ず上記の gemini コマンドを使用してください。

## 特徴

- Google の Gemini AI による高精度な検索結果
- 最新の情報にアクセス可能
- 自然言語クエリに対応

## 関連コマンド

| コマンド | 説明 |
|----------|------|
| `/commit-and-pr` | コミット & PR作成 |
| `/push` | コミット & プッシュ |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yh-05) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
