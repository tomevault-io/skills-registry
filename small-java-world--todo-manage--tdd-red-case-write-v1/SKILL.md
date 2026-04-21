---
name: tdd-red
description: > Use when this capability is needed.
metadata:
  author: small-java-world
---

# 使い方
- 最初は「落ちるテストを1つだけ」用意する（受入条件は1点）
- Green 化では「そのテストだけを通す最小実装」を行う
- その後に重複除去・命名・設計整合をリファクタで行う

## 手順
1. RED: 失敗する最小テストを1つだけ追加
2. GREEN: そのテストだけ通す最小実装
3. REFACTOR: 重複排除/命名/設計整合を実施

## 例
- `examples/red_case_sample.py`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/small-java-world) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
