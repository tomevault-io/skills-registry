---
name: ios-project-info
description: iOS プロジェクトのスキーム・ターゲット・シミュレータ・ビルド設定を XcodeBuildMCP 経由で表示。「scheme」「target」「simulator」「destination」「bundle ID」「build settings」「スキーム一覧」「ターゲット一覧」「シミュレータ一覧」「ビルド設定」などのキーワードで自動適用。 Use when this capability is needed.
metadata:
  author: no-problem-dev
---

# iOS プロジェクト情報（XcodeBuildMCP）

プロジェクトの構成情報を XcodeBuildMCP 経由で取得する。

## 方式

xbm-project-setup サブエージェントに委譲し、コンテキストを隔離する。

## ワークフロー

### プロジェクト情報取得

```
Agent(
  subagent_type: "ios-dev:xbm-project-setup",
  prompt: "以下のプロジェクトの情報を取得してください。\nプロジェクトパス: <path>\n取得内容: <schemes / simulators / build settings / bundle id / all>"
)
```

### セッションデフォルト設定

開発セッションの開始時にデフォルトを設定し、以降のビルド・テストでパラメータ省略を可能にする:

```
Agent(
  subagent_type: "ios-dev:xbm-project-setup",
  prompt: "以下のプロジェクトのセッションデフォルトを設定してください。\nプロジェクトパス: <path>\nスキーム: <scheme>\nシミュレータ: <name>"
)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/no-problem-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
