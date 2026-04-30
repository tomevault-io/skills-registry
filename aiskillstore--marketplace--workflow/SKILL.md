---
name: workflow
description: Manages workflow transitions including handoffs between PM and implementation roles, and auto-fixes review comments. Use when user mentions ハンドオフ, handoff, PMに報告, 実装役に渡して, レビュー指摘を自動修正, auto-fix. Triggers: ハンドオフ, handoff, PMに報告, 実装役に渡して, 完了報告, 自動修正, auto-fix. Do not use for 2-Agent setup - use 2agent skill instead.
metadata:
  author: aiskillstore
---

# Workflow Skills

PM-実装役間のハンドオフとレビュー指摘の自動修正を担当するスキル群です。

## 含まれる小スキル

| スキル | 用途 |
|--------|------|
| auto-fix | レビュー指摘の自動修正 |
| handoff-to-impl | PM から実装役へのハンドオフ |
| handoff-to-pm | 実装役から PM への完了報告 |

## ルーティング

- 自動修正: auto-fix/doc.md
- PM→実装役: handoff-to-impl/doc.md
- 実装役→PM: handoff-to-pm/doc.md

## 実行手順

1. ユーザーのリクエストを分類
2. 適切な小スキルの doc.md を読む
3. その内容に従って実行

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
