---
name: idcounters
description: > Use when this capability is needed.
metadata:
  author: small-java-world
---

# 使い方
- scope 例: `REQ`, `REQ-001.TSK`, `REQ-001.TSK-001.SUB`
- 1 Tx 内で `SELECT ... FOR UPDATE` → `UPDATE last=last+1`
- 失敗（ユニーク違反）が発生した場合はバックオフしてリトライ

## 手順（擬似）
1. BEGIN
2. `SELECT last FROM counters WHERE scope=:scope FOR UPDATE`
3. `UPDATE counters SET last=last+1 WHERE scope=:scope`
4. COMMIT
5. `hier_id = f"{scope}-{last:03}"` を生成

## 例
- `examples/id_alloc.sql`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/small-java-world) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
