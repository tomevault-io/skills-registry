---
name: kakutey-cli-installer
description: Install the kakutey CLI tool (npm package) for operating the kakutey bookkeeping app from the command line. Run once before using other kakutey-* skills. Use when this capability is needed.
metadata:
  author: usa-tech-lab
---

# kakutey-cli-installer

kakutey CLI（npm パッケージ `kakutey`）をインストールする。

## 前提条件

| ツール | バージョン | インストール |
|--------|-----------|-------------|
| Node.js | v18.0.0 以上 | https://nodejs.org/ |

## 使い方

```bash
python3 scripts/install.py
```

## 処理の流れ

1. 前提条件（node, npm）のバージョンチェック
2. `npm install -g kakutey-cli` でグローバルインストール
3. `kakutey --version` でインストール確認

## インストール後

`kakutey health` でアプリの稼働状態を確認できる。

## 注意

- インターネット接続が必要
- npm のグローバルインストール権限が必要（権限エラーの場合は `sudo` を使用）
- macOS / Linux / Windows 対応

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/usa-tech-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
