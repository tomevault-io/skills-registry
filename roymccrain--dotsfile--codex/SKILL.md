---
name: codex
description: Codex CLIでコードレビューを実行 Use when this capability is needed.
metadata:
  author: roymccrain
---

# /codex

Codex MCPとClaude自身で並行レビューを実行するスキル。

## コマンド

- `/codex [指示]` - コードレビュー

## 実行手順

1. ユーザーのレビュー対象・観点を整理
2. 以下を**並行**で実行する:
   - `mcp__codex__codex` を `sandbox: read-only` で呼び出す
   - Agentツールで `code-reviewer` サブエージェントを起動し、同じ観点でレビュー
3. 両方の結果を統合して報告する:
   - 両者が一致する指摘 → 確度が高い
   - 片方のみの指摘 → 補足として報告
   - 矛盾する指摘 → 両方の見解を併記

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/roymccrain) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
