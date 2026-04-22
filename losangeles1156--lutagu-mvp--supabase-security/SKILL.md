---
name: supabase-security
description: > Use when this capability is needed.
metadata:
  author: losangeles1156
---

# Supabase Security & RLS Guide

本 Skill 確保所有資料庫變更皆符合專案安全性規範。

## 🛡️ 核心原則 (Core Directives)

1.  **RLS by Default**:
    - 凡是 `CREATE TABLE`，**必須** 緊接著 `ALTER TABLE ... ENABLE ROW LEVEL SECURITY;`。
    - 嚴禁裸奔的 Table。

2.  **Service Role Access**:
    - 每個 Table 必須包含一個 `Service role full access` 的 Policy。
    - 這是確保 Edge Functions 與後端腳本能正常運作的關鍵。

3.  **Idempotency (冪等性)**:
    - 撰寫 Migration 時，使用 `DROP POLICY IF EXISTS` 再 `CREATE POLICY`。
    - 使用 `DO $$ ... END $$` 區塊包裹複雜邏輯。

4.  **No Hardcoded Secrets**:
    - 嚴禁在 SQL 中寫入 API Keys 或真實用戶個資。

## 📝 標準 Policy 命名

請參考 `reference/rls-patterns.md` 獲取完整模板：

| 類型 | 命名範例 | 邏輯 |
| :--- | :--- | :--- |
| **System** | Service role full access | `auth.role() = 'service_role'` |
| **Public** | Allow public read access | `true` (僅限 SELECT) |
| **User** | Users manage own data | `auth.uid() = user_id` |

## 🔗 詳細資源

- [RLS Patterns & Snippets](./reference/rls-patterns.md)
- [Audit Logging Setup](./reference/rls-patterns.md#4-audit-logging-security-events)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/losangeles1156) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
