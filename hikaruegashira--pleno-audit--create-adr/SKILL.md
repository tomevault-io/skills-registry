---
name: create-adr
description: > Use when this capability is needed.
metadata:
  author: hikaruegashira
---

# ADR作成

## ルール

- **ストック情報のみ**: リファクタリング手順やプロセスガイドは記録しない
- フロー情報（段階的手順、一時的な計画）はgit historyやCLAUDE.mdに任せる

## 手順

1. `docs/adr/README.md` を読み、現在の最大番号を取得
2. 次の番号で `docs/adr/{NNN}-{slug}.md` を作成

```markdown
# ADR-{NNN}: {タイトル}

## Status

Accepted

## Context

なぜこの決定が必要か。背景と制約。

## Decision

何を決定したか。具体的な方針。

## Consequences

この決定により何が変わるか。トレードオフ。
```

3. `docs/adr/README.md` のテーブル末尾にエントリを追加

```markdown
| [{NNN}](./{NNN}-{slug}.md) | {タイトル} | Accepted |
```

## 判断基準: ADRに記録すべきか

記録する: 技術選定、パッケージ構造、機能設計方針、外部制約への対応
記録しない: リファクタリングの各ステップ、一時的な計画、開発プロセス

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hikaruegashira) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
