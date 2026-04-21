---
name: cas
description: > Use when this capability is needed.
metadata:
  author: small-java-world
---

# 使い方
- 修正コミットの差分（patch）を CAS へ保存し、task に role:"patch" でリンク
- テスト実行ログも CAS へ保存し、task に role:"log" でリンク

## 手順
1. 差分パッチを CAS に保存（URI を得る）
2. `POST /tasks/{hid}/links` に `{ uri, role:"patch" }` で登録
3. テストログも CAS に保存し、`role:"log"` で登録

## 例
- `examples/review_links.http`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/small-java-world) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
