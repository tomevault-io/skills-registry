---
name: accessing-google-workspace
description: > Use when this capability is needed.
metadata:
  author: thkt
---

# Google Workspaceアクセス

CLI経由でGoogle SheetsとDocsにアクセスする。

## URL検出

| パターン                          | タイプ        |
| --------------------------------- | ------------- |
| `docs.google.com/spreadsheets/d/` | Google Sheets |
| `docs.google.com/document/d/`     | Google Docs   |

## コマンド

### Google Sheets

```bash
# CSV形式
gsheet "URL"

# JSONL形式（構造化データ向け）
gsheet "URL" json
```

### Google Docs

```bash
# テキスト形式
gdoc "URL"

# Markdown形式
gdoc "URL" md
```

## フォーマット選択

| データタイプ      | 推奨フォーマット     |
| ----------------- | -------------------- |
| 表形式データ      | `json` (JSONL)       |
| ドキュメント/仕様 | `md` (Markdown)      |
| シンプルな取得    | デフォルト (CSV/txt) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thkt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
