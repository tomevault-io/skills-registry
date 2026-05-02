---
name: guide
description: LLM Memory 完整使用指南 - 使用 llm-memory MCP 工具时必须调用此 Skill。提供计划(Plan)、待办(Todo)、记忆(Memory)的完整管理指南，包括新对话初始化、项目规划、任务管理、知识记录等工作流。Use when: llm-memory MCP is available, new conversation starts, need to manage plans/todos/memories. Use when this capability is needed.
metadata:
  author: xiaolfeng
---

# LLM Memory 使用指南

智能工作流管理助手，实现跨对话的上下文持久化。

## 核心概念

### 三层数据模型

| 层级 | 用途 | 命名格式 | 生命周期 |
|------|------|----------|----------|
| **Plan** | 长期项目计划（>3步骤） | `plan-<项目>` | 数天~数周 |
| **Todo** | 短期任务（<1天） | `todo-<项目>-<任务>` | 数小时~1天 |
| **Memory** | 知识库/决策记录 | `mem-<主题>` | 永久 |

### 状态定义

**Todo 状态**：
- `0` = 待处理
- `1` = 进行中
- `2` = 已完成
- `3` = 已取消

**Plan 状态**（根据 progress 自动转换）：
- `0%` → pending（待开始）
- `1-99%` → in_progress（进行中）
- `100%` → completed（已完成）

### 优先级

- `4` = 🔴 紧急（Bug/阻塞）
- `3` = 🟠 高（核心功能）
- `2` = 🟡 中（默认）
- `1` = 🟢 低（优化/文档）

---

## 新对话初始化

**每次新对话必须执行以下步骤：**

```javascript
// Step 1: 加载计划列表
const plans = await plan_list({ scope: "all" });

// Step 2: 加载任务列表
const todos = await todo_list({ scope: "all" });

// Step 3: 展示当前状态
// - 进行中的计划
// - 待处理/进行中的任务
// - 相关记忆（如需要）
```

**输出格式示例**：
```
📊 项目状态

📋 计划 (2 进行中):
  • [plan-user-auth] 用户认证系统 (45%)
  • [plan-api-refactor] API 重构 (20%)

📝 待办 (5 待处理):
  🔴 [todo-auth-jwt] JWT 集成 - 进行中
  🟠 [todo-auth-test] 集成测试 - 待处理
  ...
```

---

## MCP 工具概览

### Plan 管理
- `plan_list` - 列出所有计划
- `plan_create` - 创建新计划
- `plan_get` - 获取计划详情
- `plan_update` - 更新计划进度

### Todo 管理
- `todo_list` - 列出所有任务
- `todo_batch_create` - 批量创建任务
- `todo_batch_start` - 批量开始任务
- `todo_batch_complete` - 批量完成任务
- `todo_batch_cancel` - 批量取消任务
- `todo_batch_update` - 批量更新任务
- `todo_final` - 清理所有任务

### Memory 管理
- `memory_list` - 列出所有记忆
- `memory_search` - 搜索记忆
- `memory_create` - 创建记忆
- `memory_get` - 获取记忆详情
- `memory_update` - 更新记忆
- `memory_delete` - 删除记忆

### Group 管理
- `group_add_path` - 添加路径到组

详见：[MCP 工具详解](./references/mcp-tools.md)

---

## 命名规范

```
Plan:   plan-<项目>           → plan-user-auth
Todo:   todo-<项目>-<任务>    → todo-auth-login, todo-auth-jwt
Memory: mem-<主题>            → mem-jwt-decision, mem-api-standard

规则：
- 全小写
- 使用连字符 `-` 分隔
- 最少 3 个字符
- 禁止特殊字符
```

### 关联规则

Plan 与 Todo 通过 code 前缀关联：
```
plan-auth  ↔  todo-auth-*（所有以 todo-auth- 开头的任务）

进度计算 = 已完成 Todo 数 / 总 Todo 数 × 100%
```

---

## 作用域

### 创建时
- **默认**：项目级别（当前路径）
- **不要**使用 `global: true` 除非确实需要全局可见

### 查询时
- 使用 `scope: "all"` 获取当前可见的所有数据
- `scope: "personal"` - 仅当前路径
- `scope: "group"` - 仅当前小组
- `scope: "global"` - 仅全局数据

---

## Agent 协作

### 任务分配标注

在 todo description 中标注负责的 Agent：
```
[Task-A] 任务描述  → Task Agent A 负责
[Task-B] 任务描述  → Task Agent B 负责
[Main] 任务描述    → 主 Agent 负责
```

### 并发执行原则

1. **文件隔离**：不同 Agent 处理不同文件，避免冲突
2. **状态隔离**：通过 `todo_batch_start` 锁定任务
3. **进度同步**：完成后统一更新 Plan 进度

详见：[工作流指南](./references/workflows.md)

---

## 典型工作流

### 1. 新项目启动

```javascript
// 1. 创建计划
await plan_create({
  code: "plan-user-auth",
  title: "用户认证系统",
  description: "实现完整的用户认证功能",
  content: "## 目标\n- 登录/注册\n- JWT 认证\n- 权限管理"
});

// 2. 创建任务列表
await todo_batch_create({
  items: [
    { code: "todo-auth-login", title: "实现登录功能", priority: 3 },
    { code: "todo-auth-register", title: "实现注册功能", priority: 3 },
    { code: "todo-auth-jwt", title: "JWT 集成", priority: 2 },
    { code: "todo-auth-test", title: "集成测试", priority: 2 }
  ]
});
```

### 2. 执行任务

```javascript
// 开始任务
await todo_batch_start({ codes: ["todo-auth-login"] });

// ... 执行代码编写 ...

// 完成任务
await todo_batch_complete({ codes: ["todo-auth-login"] });

// 更新计划进度
await plan_update({ code: "plan-user-auth", progress: 25 });
```

### 3. 记录知识

```javascript
// 记录技术决策
await memory_create({
  code: "mem-auth-jwt-decision",
  title: "JWT vs Session 选型决策",
  content: "## 决策\n选择 JWT\n\n## 原因\n- 无状态\n- 分布式友好\n- 前后端分离",
  category: "技术决策",
  tags: ["auth", "jwt", "session"]
});
```

### 4. 查询历史

```javascript
// 搜索相关记忆
const results = await memory_search({ keyword: "jwt" });

// 获取详情
const detail = await memory_get({ code: "mem-auth-jwt-decision" });
```

详见：[使用示例](./references/examples.md)

---

## 最佳实践

1. **新对话必初始化**：始终先调用 `plan_list` + `todo_list`
2. **及时更新状态**：任务开始/完成时立即更新
3. **进度同步**：批量完成任务后更新 Plan 进度
4. **知识沉淀**：重要决策和经验及时记录到 Memory
5. **命名一致**：遵循命名规范，保持关联性

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xiaolfeng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
