---
name: xai-x-search
description: xAI Grok APIでX（旧Twitter）を検索。「Xで検索」「Xで調べて」「ツイートを検索」「X上の投稿を探して」「Twitterで検索」時に使用 Use when this capability is needed.
metadata:
  author: boxp
---

# X（旧Twitter）検索スキル

xAI GrokのX検索機能を使用してX上の投稿を検索します。

## 実行方法

**このスキルはメインコンテキストを消費しないよう、必ずTaskツール（subagent_type=Bash）で実行すること。**

```
Task tool:
  subagent_type: Bash
  prompt: |
    bash /home/boxp/.claude/skills/xai-x-search/scripts/search.sh $ARGUMENTS
```

Taskの結果を受け取ったら、内容を日本語で要約してユーザーに提示する。

## 環境変数

- `XAI_API_KEY`: xAI APIキー（必須）

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/boxp) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
