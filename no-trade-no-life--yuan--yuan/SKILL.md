---
name: vendor-implementation
description: 基于Hyperliquid成功实现经验，为新交易所供应商提供Yuan框架集成指南。使用此技能当需要为新的交易所创建供应商实现，包括项目结构设计、API集成、核心服务实现和最佳实践。适用于交易所API集成、金融系统开发、微服务架构设计。 Use when this capability is needed.
metadata:
  author: No-Trade-No-Life
---

# Vendor Implementation

为新的交易所供应商提供完整的 Yuan 框架集成指南，基于 Hyperliquid、Aster、OKX 等成功实现经验。

## 使用场景

使用此技能当需要：

- 为新交易所创建供应商实现
- 设计微服务架构和 API 集成
- 实现交易系统核心功能（账户、订单、市场数据）
- 遵循 Yuan 框架规范和最佳实践

## 核心原则

### 简洁至上

上下文窗口是公共资源。技能与系统提示、对话历史、其他技能的元数据和用户请求共享上下文。

**默认假设：Claude 已经很智能。**只添加 Claude 不具备的上下文。对每条信息进行挑战："Claude 真的需要这个解释吗？"，"这个段落是否值得其令牌成本？"

优先使用简洁示例而非冗长解释。

### 设置适当的自由度

将特异性级别与任务的脆弱性和可变性匹配：

**高自由度（文本指令）**：当多种方法都有效、决策依赖上下文，或启发式指导方法时使用。

**中等自由度（伪代码或带参数的脚本）**：当存在首选模式、某些变化可接受，或配置影响行为时使用。

**低自由度（特定脚本，少数参数）**：当操作易错、一致性至关重要，或必须遵循特定序列时使用。

## 标准目录结构

```
apps/vendor-{exchange}/src/
├── api/                           # API层
│   ├── client.ts                # HTTP客户端设置
│   ├── public-api.ts            # 公共API端点
│   ├── private-api.ts           # 私有API端点
│   └── types.ts                 # TypeScript类型定义
├── services/                     # 服务层
│   ├── accounts/                # 账户服务
│   │   └── perp.ts              # 永续账户信息
│   ├── orders/                  # 订单管理
│   │   ├── submitOrder.ts       # 订单提交
│   │   ├── cancelOrder.ts       # 订单取消
│   │   ├── modifyOrder.ts       # 订单修改
│   │   └── listOrders.ts         # 订单列表
│   ├── markets/                 # 市场数据
│   │   ├── quote.ts             # 实时报价
│   │   ├── product.ts           # 产品信息
│   │   ├── ohlc.ts              # K线数据
│   │   └── interest-rate.ts     # 利率数据
│   ├── account-actions-with-credential.ts  # 账户RPC
│   ├── order-actions-with-credential.ts     # 订单RPC
│   └── fill-history.ts          # 成交记录（如果支持）
├── utils.ts                      # 工具函数
├── sign.ts                       # 请求签名
├── index.ts                      # 主入口
├── cli.ts                        # CLI入口
├── AGENTS.md                     # Agent文档
├── SESSION_NOTES.md              # 会话记录
└── package.json                  # 依赖
```

## 核心实现模式

### 缓存模式（使用 createCache）

```typescript
import { createCache } from '@yuants/cache';

const CACHE_TTL = 60_000;

const metaCache = createCache<Map<string, AssetInfo>>(
  async () => {
    console.info(`[${formatTime(Date.now())}] 刷新交易所元数据缓存`);
    const data = await fetchExchangeMetadata();
    return processData(data);
  },
  { expire: CACHE_TTL },
);

export const getAssetInfo = async (symbol: string) => {
  const cache = await metaCache.query('meta');
  return cache.get(symbol);
};
```

### 服务注册模式

```typescript
export const submitOrder = async (credential: ICredential, order: IOrder) => {
  console.info(`[${formatTime(Date.now())}] 提交订单: ${order.product_id}`);

  try {
    const payload = buildOrderPayload(order);
    const result = await placeOrder(credential, payload);

    if (!result.status || result.status !== 'ok') {
      throw new Error(`订单提交失败: ${result.error}`);
    }

    const orderId = extractOrderId(result);
    console.info(`[${formatTime(Date.now())] 订单提交成功: ${orderId}`);
    return { order_id: `${orderId}` };
  } catch (error) {
    const errorMessage = error instanceof Error ? error.message : '未知错误';
    console.error(`[${formatTime(Date.now())}] 订单提交失败: ${errorMessage}`);
    throw new Error(`订单提交失败: ${errorMessage}`);
  }
};
```

### RPC 注册模式

```typescript
import { provideOrderActionsWithCredential } from '@yuants/data-order';

provideOrderActionsWithCredential<ICredential>(
  Terminal.fromNodeEnv(),
  'EXCHANGE',
  {
    type: 'object',
    required: ['private_key', 'address'],
    properties: {
      private_key: { type: 'string' },
      address: { type: 'string' },
    },
  },
  {
    submitOrder,
    cancelOrder,
    modifyOrder,
    listOrders,
  },
);
```

## 实现检查清单

### ✅ 必需核心功能

1. **账户信息**

   - [ ] 现货账户余额和持仓
   - [ ] 永续账户保证金和持仓
   - [ ] 多账户类型支持

2. **订单管理**

   - [ ] 订单提交（市价/限价）
   - [ ] 订单取消
   - [ ] 订单修改（如果支持）
   - [ ] 待成交订单列表

3. **市场数据**

   - [ ] 实时报价
   - [ ] K 线数据
   - [ ] 产品信息
   - [ ] 利率数据（如适用）

4. **API 集成**
   - [ ] 公共 API 端点
   - [ ] 带认证的私有 API
   - [ ] 错误处理和重试逻辑

### 🎯 高级功能

1. **交易历史**

   - [ ] 成交记录实现
   - [ ] 交易记录

2. **转账支持**
   - [ ] 内部转账
   - [ ] 提现
   - [ ] 充值地址

## 质量标准

### 代码质量

- ✅ TypeScript 严格模式
- ✅ 全面的错误处理
- ✅ 单元测试覆盖
- ✅ 集成测试验证

### 性能

- ✅ `createCache`高效缓存
- ✅ API 速率限制
- ✅ 批量操作优化

### 文档

- ✅ AGENTS.md 实现细节
- ✅ SESSION_NOTES.md 变更跟踪
- ✅ API 文档集成
- ✅ 完整 README

## 参考实现

### 学习这些示例

1. **Hyperliquid**: 原生 modify order API + 完整功能集
2. **Aster**: 清晰结构 + 现货/永续分离
3. **OKX**: 综合功能 + 转账支持

### 关键学习点

1. **目录结构**: vendor-aster 的可扩展模式
2. **缓存策略**: `createCache` vs 手动管理
3. **错误处理**: 跨服务的一致模式
4. **类型安全**: 完整 TypeScript 接口
5. **文档维护**: AGENTS.md + SESSION_NOTES.md

## 关键经验

### 1. 使用原生 API

优先使用交易所的原生 API 而非模拟方案：

- 使用 modify order 而非 cancel + place new
- 保持订单优先级，减少 API 调用

### 2. 日志标准

```typescript
console.info(`[${formatTime(Date.now())] 操作成功`);
console.error(`[${formatTime(Date.now())] 操作失败: ${error.message}`);
```

### 3. 类型安全

```typescript
interface IExchangeResponse {
  status: string;
  data?: any;
  error?: string;
}

function validateResponse<T>(response: IExchangeResponse): T {
  if (response.status !== 'ok') {
    throw new Error(response.error || 'API调用失败');
  }
  return response.data as T;
}
```

### 4. 文档维护

SESSION_NOTES.md 记录每次重要变更，包括：

- 技术决策 (D1, D2, ...)
- 架构变更
- 错误解决记录
- TODO 和风险项

## 常见陷阱

1. **API 限制** - 始终检查官方文档
2. **速率限制** - 实现适当节流
3. **认证安全** - 绝不在代码中硬编码密钥
4. **数据一致性** - 处理部分失败场景
5. **测试依赖** - 正确模拟外部服务

## 成功指标

成功的供应商实现应该：

1. ✅ 通过所有集成测试
2. ✅ 优雅处理 API 限制
3. ✅ 提供一致的错误消息
4. ✅ 支持所有必需 RPC 方法
5. ✅ 包含全面文档
6. ✅ 遵循既定编码模式
7. ✅ 可维护和可扩展

## 进阶资源

- **工作流模式**: references/workflows.md
- **输出模式**: references/output-patterns.md
- **错误处理**: references/error-handling.md

---

_基于 Hyperliquid、Aster、OKX 供应商实现经验生成_

---
> Source: [No-Trade-No-Life/Yuan](https://github.com/No-Trade-No-Life/Yuan) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
