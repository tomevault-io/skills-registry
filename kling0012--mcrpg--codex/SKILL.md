---
name: codex
description: 実行するタスクの説明（省略時は自己紹介のみ） Use when this capability is needed.
metadata:
  author: kling0012
---

# Codex - コーディング担当エージェント

Codex（中上級者のコーディングエージェント）を起動します。

## 役割
- コードの実装・リード
- 設計・アーキテクチャの検討
- 技術的な課題解決

## 特性
- 作業能力が高い中上級者
- Java、Minecraftプラグイン開発に精通
- テスト駆動開発、リファクタリングが得意

{{#if task}}
## タスク
{{task}}

以下のBashコマンドでCodexを実行してください：

```bash
codex exec --full-auto --sandbox read-only --cd /workspaces/java/Minecraft-Java-Plaugin-forRPG "{{{task}}}"
```

結果をログファイルに保存する場合：

```bash
mkdir -p .sprint/outputs
codex exec --full-auto --sandbox read-only --cd /workspaces/java/Minecraft-Java-Plaugin-forRPG "{{{task}}}" > .sprint/outputs/codex.log 2>&1 &
```
{{else}}
以下のBashコマンドでCodexを実行してください：

```bash
codex exec --full-auto --sandbox read-only --cd /workspaces/java/Minecraft-Java-Plaugin-forRPG "自己紹介と、準備ができたことを報告してください。"
```
{{/if}}

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kling0012) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
