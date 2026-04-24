---
name: alicloud-security-center-sas-test
description: Minimal smoke test for Security Center SAS skill. Validate read-only query flow. Use when this capability is needed.
metadata:
  author: cinience
---

Category: test

# SAS Minimal Viable Test

## Prerequisites

- AK/SK and region are configured.
- GoalsSkill: `skills/security/host/alicloud-security-center-sas/`。

## Test Steps

1) 获取 SAS 的 API 列表。
2) 执行一个只读查询 API。
3) 记录成功/失败及错误码。

## Expected Results

- 请求链路可达，返回可解析 JSON。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cinience) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
