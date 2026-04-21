---
name: external-integration
description: Use when integrating with external APIs, third-party SDKs, or libraries - covers verification workflow before writing any integration code
metadata:
  author: aquarius-wing
---

# 外部系统集成

## Overview

对接任何外部系统（API、SDK、第三方库）时必须遵循的验证流程。

**核心原则：** 不假设，要验证。文档要逐字对照，示例数据要实际检查，版本要确认。

## When to Use

**触发场景：**
- 对接远端 API（REST、GraphQL 等）
- 使用第三方 SDK 或库
- 升级已有依赖的版本
- 发现集成代码行为异常

## 对接流程

```
1. 获取文档 → 2. 获取示例数据 → 3. 逐字验证 → 4. 编写映射代码
```

### Step 1: 获取官方文档

**API 对接：**
- 使用 WebFetch 获取 API 文档页面
- 找到具体接口的参数说明和响应格式

**SDK/库使用：**
- 检查 package.json 中的实际版本号
- WebFetch 获取该版本的官方文档
- 特别关注 CHANGELOG 和 Breaking Changes

### Step 2: 获取示例数据

**必须先拿到真实响应：**
```bash
# API：用 curl 或代码获取一条真实数据
curl "https://api.example.com/endpoint?param=value"

# 库：写一个最小示例验证行为
```

**打印并保存示例数据，用于后续对照。**

### Step 3: 逐字验证

对照文档和示例数据，检查每个字段：

| 检查项 | 常见坑点 |
|-------|---------|
| 参数名 | `?wallet=` vs `?address=` |
| 字段名 | `proxyWallet` vs `proxy_wallet` |
| 字段类型 | 数字是 `string` 还是 `number` |
| 时间戳单位 | 秒级 vs 毫秒级 |
| 枚举大小写 | `BUY` vs `buy` |
| 可空字段 | 哪些字段可能返回 `null` 或缺失 |

### Step 4: 编写映射代码

基于验证结果编写代码，处理所有边界情况。

---

## 测试策略

**核心原则：外部集成模块必须使用集成测试，禁止用 mock 做单元测试。**

### 理由

外部系统集成的核心价值是：
1. 正确构造请求（URL、参数、Header）
2. 正确解析响应（字段映射、类型转换）
3. 正确处理错误（网络超时、API 变更）

这些只有真实调用才能验证，mock 测试无法发现：
- API 端点变更
- 响应格式变化
- 认证机制更新
- 网络问题

### 测试文件结构

```
tests/
└── integration/
    └── packages/db/
        ├── polymarket-api.test.ts  # 真实调用 Polymarket API
        ├── activities.test.ts       # 真实操作 Supabase
        └── order-placement.test.ts  # 真实调用 CLOB API（测试环境）
```

### 测试环境要求

- 使用真实的测试环境（如本地 Supabase、Polymarket testnet）
- 测试前后清理测试数据
- 使用专用测试账户，避免影响生产数据

---

## Reference

- [API 对接检查清单](./api-checklist.md)
- [SDK/库使用检查清单](./sdk-checklist.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aquarius-wing) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
