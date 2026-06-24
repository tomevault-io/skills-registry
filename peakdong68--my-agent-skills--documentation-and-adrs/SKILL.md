---
name: documentation-and-adrs
description: 记录决策与文档(ADR)。用于制定架构决策、修改公开 API、发布功能，或需要记录上下文以供未来的工程师和智能体理解代码库时。 Use when this capability is needed.
metadata:
  author: peakdong68
---

# 文档与 ADR

## 概述

记录决策，而不仅仅是代码。最有价值的文档能捕捉到 _为什么_ —— 也就是导致某项决策的上下文、约束和权衡。代码展示了 _做了什么_；文档则解释 _为什么这么做_ 以及 _考虑了哪些替代方案_。这些上下文对于未来在代码库中工作的人和智能体都至关重要。

## 何时使用

- 做出一项重大的架构决策
- 在多个竞争方案之间做选择
- 添加或修改公开 API
- 发布一个会改变用户面向行为的功能
- 让新团队成员（或智能体）融入项目
- 当你发现自己在反复解释同一件事时

**何时不应使用：** 不要为显而易见的代码写文档。不要添加只是在复述代码本身的注释。不要为一次性的原型写文档。

## 架构决策记录 (ADR)

ADR 能捕捉重大技术决策背后的推理。它们是你能撰写的价值最高的文档。

### 何时编写 ADR

- 选择一个框架、库或主要依赖项
- 设计数据模型或数据库 schema
- 选择认证策略
- 决定 API 架构（REST vs GraphQL vs tRPC）
- 在构建工具、托管平台或基础设施之间做选择
- 任何撤销成本高昂的决策

### ADR 模板

将 ADR 存储在 docs/decisions/ 目录中，并按顺序编号：

```markdown
# ADR-001：使用 PostgreSQL 作为主数据库

## 状态

已接受 | 被 ADR-XXX 替代 | 已废弃

## 日期

2025-01-15

## 背景

我们需要为任务管理应用选择一个主数据库。关键需求：

- 关系型数据模型（用户、任务、团队之间存在关联）
- 针对任务状态变更的 ACID 事务
- 支持对任务内容的全文搜索
- 有可用的托管服务（小型团队，运维能力有限）

## 决策

使用 PostgreSQL 并搭配 Prisma ORM。

## 考虑的替代方案

### MongoDB

- 优点：灵活的 schema，易于上手
- 缺点：我们的数据本质上是关系型的；需要手动管理关联
- 拒绝原因：在文档存储中处理关系型数据会导致复杂的联结或数据冗余

### SQLite

- 优点：零配置，嵌入式，读取速度快
- 缺点：并发写入支持有限，没有可用于生产环境的托管服务
- 拒绝原因：不适合在生产环境中使用的多用户 web 应用

### MySQL

- 优点：成熟，被广泛支持
- 缺点：PostgreSQL 在 JSON 支持、全文搜索及生态工具链方面更优
- 拒绝原因：PostgreSQL 更贴合我们的功能需求

## 后果

- Prisma 提供类型安全的数据库访问和迁移管理
- 我们可以使用 PostgreSQL 的全文搜索，而无需额外引入 Elasticsearch
- 团队需要了解 PostgreSQL（这是通用技能，风险低）
- 在托管服务上运行（Supabase、Neon 或 RDS）
```

### ADR 生命周期

```
PROPOSED → ACCEPTED → （SUPERSEDED 或 DEPRECATED）
```

- **不要删除旧的 ADR。** 它们能承载历史上下文。
- 当决策发生变化时，编写一个新的 ADR，并引用和替代旧的。

## 内联文档

### 何时写注释

注释要为 _为什么_ 而写，而不是为 _做了什么_：

```typescript
// 不好：复述代码
// 将计数器加 1
counter += 1;

// 好：解释非显而易见的意图
// 速率限制使用滑动窗口 —— 在窗口边界重置计数器，
// 而非按固定周期重置，以防止窗口边界的突发攻击
if (now - windowStart > WINDOW_SIZE_MS) {
  counter = 0;
  windowStart = now;
}
```

### 何时不应写注释

```typescript
// 不要为不言自明的代码写注释
function calculateTotal(items: CartItem[]): number {
  return items.reduce((sum, item) => sum + item.price * item.quantity, 0);
}

// 不要留下 TODO 注释，若这件事现在就应做
// TODO: 添加错误处理  ← 直接加上就好

// 不要留下被注释掉的代码
// const oldImplementation = () => { ... }  ← 删掉它，git 有历史记录
```

### 记录已知的陷阱

```typescript
/**
 * 重要：此函数必须在第一次渲染前调用。
 * 若在水合之后调用，会导致未样式化内容闪烁，
 * 因为在 SSR 期间主题上下文不可用。
 *
 * 完整的设计原理请参阅 ADR-003。
 */
export function initializeTheme(theme: Theme): void {
  // ...
}
```

## API 文档

对于公开 API（REST、GraphQL、库接口）：

### 与类型内联（TypeScript 推荐方式）

```typescript
/**
 * 创建一个新任务。
 *
 * @param input - 任务创建数据（标题必填，描述可选）
 * @returns 包含服务端生成的 ID 和时间戳的已创建任务
 * @throws {ValidationError} 当标题为空或超过 200 个字符时
 * @throws {AuthenticationError} 当用户未认证时
 *
 * @example
 * const task = await createTask({ title: 'Buy groceries' });
 * console.log(task.id); // "task_abc123"
 */
export async function createTask(input: CreateTaskInput): Promise<Task> {
  // ...
}
```

### REST API 的 OpenAPI / Swagger

```yaml
paths:
  /api/tasks:
    post:
      summary: 创建任务
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateTaskInput'
      responses:
        '201':
          description: 任务已创建
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Task'
        '422':
          description: 验证错误
```

## README 结构

每个项目都应有一个涵盖以下内容的 README：

```markdown
# 项目名称

用一段话描述这个项目是做什么的。

## 快速开始

1. 克隆仓库
2. 安装依赖：`npm install`
3. 设置环境：`cp .env.example .env`
4. 启动开发服务器：`npm run dev`

## 命令

| 命令            | 说明           |
| --------------- | -------------- |
| `npm run dev`   | 启动开发服务器 |
| `npm test`      | 运行测试       |
| `npm run build` | 生产构建       |
| `npm run lint`  | 运行 linter    |

## 架构

简要介绍项目结构和关键设计决策。
链接到 ADR 以了解详情。

## 贡献

如何贡献、编码规范、PR 流程。
```

## 变更日志维护

对于已发布的功能：

```markdown
# 变更日志

## [1.2.0] - 2025-01-20

### 新增

- 任务共享：用户可与团队成员共享任务 (#123)
- 任务分配时的邮件通知 (#124)

### 修复

- 快速点击创建按钮时出现重复任务的问题 (#125)

### 变更

- 任务列表现在每页加载 50 项（原为 20），以提升用户体验 (#126)
```

## 面向智能体的文档

针对 AI 智能体上下文的特别考量：

- **CLAUDE.md / 规则文件** —— 记录项目约定，使智能体能遵循它们
- **规格文件** —— 保持规格更新，以便智能体构建正确的东西
- **ADR** —— 帮助智能体理解过去决策的原因（避免重新决策）
- **内联陷阱** —— 防止智能体陷入已知的陷阱

## 常见合理化借口

| 借口                          | 现实                                                                                   |
| ----------------------------- | -------------------------------------------------------------------------------------- |
| “代码自身就是文档”            | 代码展示做了什么。它不展示为什么这么做、拒绝了哪些替代方案或存在哪些约束。             |
| “我们会在 API 稳定后再写文档” | 当你为 API 编写文档时，它会更快稳定。文档是对设计的首次检验。                          |
| “没人看文档”                  | 智能体会看。未来的工程师会看。三个月后的你自己也会看。                                 |
| “ADR 是额外开销”              | 花 10 分钟写一个 ADR，可以避免六个月后为同一个决策再争论两小时。                       |
| “注释会过时”                  | 关于 _为什么_ 的注释是稳定的。关于 _做了什么_ 的注释会过时 —— 这就是你只写前者的原因。 |

## 危险信号

- 重大的架构决策没有任何书面推理
- 公开 API 没有任何文档或类型定义
- README 没有说明如何运行项目
- 代码被注释掉而不是直接删除
- TODO 注释留了好几个星期
- 存在重大架构选择的项目却没有 ADR
- 文档只是在复述代码，而不是解释意图

## 验证

完成文档记录后：

- [ ] 所有重大的架构决策都有对应的 ADR
- [ ] README 涵盖了快速开始、命令和架构概览
- [ ] API 函数均有参数和返回类型文档
- [ ] 已知的陷阱都在其起作用的位置以内联方式记录
- [ ] 没有遗留下来被注释掉的代码
- [ ] 规则文件（如 CLAUDE.md 等）是最新且准确的

---
> Source: [peakdong68/my-agent-skills](https://github.com/peakdong68/my-agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
