---
name: polymarket-api
description: Use when working with Polymarket API - profiles, activities, events, markets endpoints
metadata:
  author: aquarius-wing
---

# Polymarket API 对接

## Overview

Polymarket API 的对接指南，包含各接口的地址和关键注意事项。

**对接前必读：** 请先阅读 `external-integration` skill，遵循标准对接流程。

## 接口列表

| 接口 | 用途 | 文档 |
|-----|------|------|
| CLOB 认证 | L2 API 认证（HMAC 签名） | [clob-authentication.md](./clob-authentication.md) |
| Profile | 获取用户信息 | [profiles-api.md](./profiles-api.md) |
| Activities | 获取交易活动 | [activities-api.md](./activities-api.md) |
| Events | 获取事件信息 | [events-api.md](./events-api.md) |

## API 文档地址

- Profile: https://docs.polymarket.com/api-reference/profiles/get-public-profile-by-wallet-address
- Events: https://docs.polymarket.com/api-reference/events/get-event-by-id
- Events by slug: https://docs.polymarket.com/api-reference/events/get-event-by-slug

## 通用注意事项

### 1. 时间戳是秒级

API 中的时间戳都是秒级，不是毫秒级：

```typescript
// ❌ 响应中的时间戳转换
new Date(activity.timestamp)

// ✅ 需要乘以 1000
new Date(activity.timestamp * 1000)

// ❌ 请求中的时间戳
const ts = Date.now();

// ✅ 需要除以 1000
const ts = Math.floor(Date.now() / 1000);
```

### 2. HMAC 签名使用 URL-safe Base64

详见 [clob-authentication.md](./clob-authentication.md)，这是常见的间歇性失败原因。

### 2. 字段大小写需标准化

```typescript
// API 返回小写，数据库要求大写
const side = raw.side.toUpperCase() as 'BUY' | 'SELL'
```

### 3. 某些字段可能为空

特别是 `address` 字段，API 可能返回空值，应使用 `proxy_wallet` 作为主键。

## 数据关系

```
polymarket_user_profiles (码表)
  └── proxy_wallet (主键)

activities
  ├── proxy_wallet → profiles.proxy_wallet
  └── event_slug → events.slug

events (码表)
  └── markets[] (嵌套)
```

**注意：** activities 中的 `event_slug` 可能在 events 表中不存在（未同步），需要处理降级显示。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aquarius-wing) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
