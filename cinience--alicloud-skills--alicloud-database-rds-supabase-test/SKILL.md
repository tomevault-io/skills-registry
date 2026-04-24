---
name: alicloud-database-rds-supabase-test
description: Minimal smoke test for RDS Supabase skill. Validate endpoint reachability and one list/detail API. Use when this capability is needed.
metadata:
  author: cinience
---

Category: test

# RDS Supabase Minimal Viable Test

## Prerequisites

- AK/SK and region are configured.
- GoalsSkill: `skills/database/rds/alicloud-database-rds-supabase/`。

## Test Steps

1) 读取技能中 API 基础信息。
2) 调用一个只读列表或详情 API。
3) 记录实例数或错误码。

## Expected Results

- 可完成一次最小只读调用。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cinience) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
