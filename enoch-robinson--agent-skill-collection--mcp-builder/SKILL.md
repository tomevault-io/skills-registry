---
name: mcp-builder
description: MCP (Model Context Protocol) 服务器开发指南。当用户需要创建 MCP 服务器、集成外部 API 或服务、让 LLM 与外部系统交互时使用此技能。支持 TypeScript（推荐）和 Python。 Use when this capability is needed.
metadata:
  author: enoch-robinson
---

# MCP Server Development Guide

创建高质量的 MCP 服务器，让 LLM 能够与外部服务交互。

## 概述

MCP (Model Context Protocol) 是一种协议，允许 LLM 通过定义良好的工具与外部服务交互。MCP 服务器的质量取决于它能多好地帮助 LLM 完成实际任务。

## 技术栈推荐

- **语言**: TypeScript（推荐）- SDK 支持好，类型安全
- **传输**: Streamable HTTP（远程服务器）/ stdio（本地服务器）

## 开发流程

### 阶段 1：研究与规划

1. **理解 API**：研究目标服务的 API 文档
2. **工具选择**：优先覆盖常用API 端点
3. **命名规范**：使用清晰的前缀，如 `github_create_issue`

### 阶段 2：实现

#### 项目结构 (TypeScript)
```
my-mcp-server/
├── src/
│   ├── index.ts        # 入口文件
│   ├── tools/          # 工具定义
│   └── utils/          # 工具函数
├── package.json
└── tsconfig.json
```

#### 工具定义要点

```typescript
// 使用 Zod 定义输入 Schema
const inputSchema = z.object({
  query: z.string().describe("搜索关键词"),
  limit: z.number().optional().default(10).describe("返回数量")
});

// 工具注解
{
  readOnlyHint: true,      // 只读操作
  destructiveHint: false,  // 非破坏性
  idempotentHint: true,    // 幂等操作
}
```

### 阶段 3：测试

```bash
# TypeScript 构建
npm run build

# 使用 MCP Inspector 测试
npx @modelcontextprotocol/inspector
```

### 阶段 4：创建评估

创建 10 个复杂、真实的测试问题验证服务器效果。

## 工具设计原则

1. **清晰命名**：`service_action_target` 格式
2. **完整描述**：包含参数说明和返回类型
3. **错误处理**：提供可操作的错误信息
4. **分页支持**：大数据集支持分页

## 参考资源

- MCP 协议文档：https://modelcontextprotocol.io
- TypeScript SDK：https://github.com/modelcontextprotocol/typescript-sdk
- Python SDK：https://github.com/modelcontextprotocol/python-sdk

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/enoch-robinson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
