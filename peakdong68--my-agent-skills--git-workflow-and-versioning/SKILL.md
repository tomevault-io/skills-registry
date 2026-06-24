---
name: git-workflow-and-versioning
description: Structures git workflow practices. Use when making any code change. Use when committing, branching, resolving conflicts.对于派发并行的 Agent 子代理(subAgent)工作，使用 git worktrees 同时运行多个分支。被增量实现、代码审查、测试驱动开发、工作流验证等技能在涉及版本控制操作时内部引用。 Use when this capability is needed.
metadata:
  author: peakdong68
---

# Git 工作流与版本控制

## 概述

Git 是你的安全网。把提交(commits)看作保存点，分支(branches)看作隔离区，历史(history)看作文档。在 AI 代理高速生成代码时，严格的版本控制是使变更保持可管理、可审查、可回退的机制。

## 使用时机

始终使用。每个代码更改都会经过 Git。

## 核心原则

### 基于主干的开发（推荐）

保持 `main` 分支始终可部署。在生命周期较短（1-3 天内合并回主线）的特性分支上工作。长期存在的开发分支是隐性成本——它们会产生分歧、造成合并冲突并延迟集成。DORA 研究一致表明，基于主干的开发与高绩效工程团队相关。

```
main ──●──●──●──●──●──●──●──●──●──  (始终可部署)
        ╲      ╱  ╲    ╱
         ●──●─╱    ●──╱    ← 短期特性分支（1-3天）
```

这是推荐的默认方式。使用 gitflow 或长期分支的团队可以将这些原则（原子提交、小变更、描述性消息）适配到他们的分支模型中——提交纪律比具体的分支策略更重要。

- **开发分支是成本。** 分支每多存活一天，就会累积合并风险。
- **发布分支是可接受的。** 当你需要在主线向前推进时稳定一个版本时使用。
- **特性标志优于长期分支。** 倾向于将未完成的工作部署在标志后面，而不是在分支上保留数周。

### 1. 尽早提交，经常提交

每次成功的增量都应该有自己的提交。不要积累大量未提交的更改。

```
工作模式：
  实现切片 → 测试 → 验证 → 提交 → 下一切片

不这样做：
  实现所有功能 → 希望它能工作 → 巨型提交
```

提交是保存点。如果下一次更改破坏了某些东西，你可以立即回退到上一个已知的良好状态。

### 2. 原子提交

每个提交只做一件逻辑上独立的事情：

```
# 好：每次提交都是自包含的
git log --oneline
a1b2c3d 添加带有验证的任务创建端点
d4e5f6g 添加任务创建表单组件
h7i8j9k 连接表单到 API 并添加加载状态
m1n2o3p 添加任务创建测试（单元测试 + 集成测试）

# 坏：所有内容混在一起
git log --oneline
x1y2z3a 添加任务功能，修复侧边栏，更新依赖，重构工具函数
```

### 3. 描述性提交信息

提交信息解释*为什么*而不仅仅是*做了什么*：

```
# 好：解释意图
feat: 在注册端点添加邮箱验证

防止无效的邮箱格式进入数据库。
在路由处理程序级别使用 Zod schema 验证，
与 auth.ts 中现有的验证模式保持一致。

# 坏：描述了从 diff 中显而易见的内容
更新 auth.ts
```

**格式：**

```
<type>: <short description>

<optional body explaining why, not what>
```

**类型：**

- `feat` — 新功能
- `fix` — 问题修复
- `refactor` — 既不修复问题也不添加功能的代码更改
- `test` — 添加或更新测试
- `docs` — 仅文档
- `chore` — 工具、依赖、配置

### 4. 保持关注点分离

不要将格式更改与行为更改混在一起。不要将重构与功能混在一起。每种类型的变更都应是一个单独的提交——理想情况下还应是一个单独的 PR：

```
# 好：分离关注点
git commit -m "refactor: 将验证逻辑提取到共享工具函数"
git commit -m "feat: 在注册中添加电话号码验证"

# 坏：混合关注点
git commit -m "重构验证并添加电话号码字段"
```

**将重构与特性工作分离。** 重构变更和特性变更是两种不同的变更——分开提交。这使得每个变更在历史上更易于审查、回退和理解。小型清理（如重命名变量）可以根据审查者的酌情权包含在特性提交中。

### 5. 控制变更大小

目标是每次提交/PR 约 100 行。超过约 1000 行的变更应该拆分。请参阅 `code-review-and-quality` 中的拆分策略，了解如何分解大型变更。

```
~100 行   → 易于审查，易于撤销
~300 行   → 对于单个逻辑变更可以接受
~1000 行  → 拆分为较小的变更
```

## 分支策略

### 特性分支

```
main (始终可部署)
  │
  ├── feature/task-creation    ← 每个分支一个特性
  ├── feature/user-settings    ← 并行工作
  └── fix/duplicate-tasks      ← 问题修复
```

- 从 `main`（或团队的默认分支）创建分支
- 保持分支的生命周期较短（1-3 天内合并）——长期分支是隐性成本
- 合并后删除分支
- 对于未完成的特性，优先使用特性标志而不是长期分支

### 分支命名

```
feature/<short-description>   → feature/task-creation
fix/<short-description>       → fix/duplicate-tasks
chore/<short-description>     → chore/update-deps
refactor/<short-description>  → refactor/auth-module
```

## 使用 Worktrees

对于并行的 Agent 子代理(subAgent)工作，使用 git worktrees 同时运行多个分支：

```bash
# 为功能分支创建 worktree
git worktree add ../project-feature-a feature/task-creation
git worktree add ../project-feature-b feature/user-settings

# 每个 worktree 是一个拥有自己分支的独立目录
# 代理可以并行工作而互不干扰
ls ../
  project/              ← main 分支
  project-feature-a/    ← task-creation 分支
  project-feature-b/    ← user-settings 分支

# 完成后，合并并清理
git worktree remove ../project-feature-a
```

优点：

- 多个代理可以同时处理不同的特性
- 无需切换分支（每个目录有自己的分支）
- 如果一个实验失败，删除 worktree ——什么都不会丢失
- 更改在被显式合并之前是隔离的

## 保存点模式

```
代理开始工作
    │
    ├── 进行更改
    │   ├── 测试通过？ → 提交 → 继续
    │   └── 测试失败？ → 回退到上一个提交 → 调查
    │
    ├── 进行另一个更改
    │   ├── 测试通过？ → 提交 → 继续
    │   └── 测试失败？ → 回退到上一个提交 → 调查
    │
    └── 特性完成 → 所有提交形成干净的历史
```

这种模式意味着你永远不会丢失超过一个工作增量。如果代理偏离了轨道，`git reset --hard HEAD` 会将你带回到上一个成功状态。

## 变更摘要

在任何修改之后，提供一个结构化的摘要。这使得审查更容易，记录了范围纪律，并暴露了非预期的变更：

```
已进行的变更：
- src/routes/tasks.ts: 向 POST 端点添加验证中间件
- src/lib/validation.ts: 使用 Zod 添加 TaskCreateSchema

我有意未触及的内容：
- src/routes/auth.ts: 存在类似的验证缺口，但超出范围
- src/middleware/error.ts: 错误格式可以改进（单独的任务）

潜在关注点：
- Zod schema 很严格——拒绝额外字段。确认这是期望的行为。
- 添加了 zod 作为依赖项（压缩后 72KB）——已在 package.json 中
```

这种模式能及早发现错误的假设，并为审查者提供清晰的变更地图。“未做修改”部分尤其重要——它表明你遵守了范围纪律，没有进行未经请求的翻修。

## 提交前检查清单

每次提交之前：

```bash
# 1. 检查即将提交的内容
git diff --staged

# 2. 确保没有秘密信息
git diff --staged | grep -i "password\|secret\|api_key\|token"

# 3. 运行测试
npm test

# 4. 运行 lint
npm run lint

# 5. 运行类型检查
npx tsc --noEmit
```

使用 git hooks 自动化执行：

```json
// package.json (using lint-staged + husky)
{
  "lint-staged": {
    "*.{ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{json,md}": ["prettier --write"]
  }
}
```

## 处理生成的文件

- 仅在项目期望时**提交生成的文件**（例如 `package-lock.json`、Prisma 迁移）
- **不要提交**构建输出（`dist/`、`.next/`）、环境文件（`.env`）或 IDE 配置（除非共享的 `.vscode/settings.json`）
- **拥有一个 `.gitignore`** 文件，涵盖：`node_modules/`、`dist/`、`.env`、`.env.local`、`*.pem`

## 使用 Git 进行调试

```bash
# 查找引入 bug 的提交
git bisect start
git bisect bad HEAD
git bisect good <已知良好的提交>
# Git 会检出中间点；在每一步运行测试以缩小范围

# 查看最近的变更
git log --oneline -20
git diff HEAD~5..HEAD -- src/

# 查找最后更改特定行的人
git blame src/services/task.ts

# 在提交消息中搜索关键词
git log --grep="validation" --oneline
```

## 常见合理化借口及现实

| 合理化借口              | 现实情况                                                                          |
| ----------------------- | --------------------------------------------------------------------------------- |
| “等功能完成了我再提交”  | 一个巨大的提交根本无法审查、调试或回退。为每个切片提交。                          |
| “提交信息不重要”        | 提交信息就是文档。未来的你（以及未来的代理）需要理解改了什么以及为什么。          |
| “我以后会将其压缩的”    | 压缩会破坏开发叙事。从一开始就倾向于干净的增量提交。                              |
| “分支会增加开销”        | 短期分支是免费的，并能防止冲突的工作相互碰撞。长期分支才是问题——应在1-3天内合并。 |
| “我稍后再拆分这个更改”  | 大型更改更难审查，部署风险更高，也更难回退。在提交前就拆分，而不是之后。          |
| “我不需要 `.gitignore`” | 直到包含生产机密的 `.env` 被提交。立即设置好。                                    |

## 危险信号

- 大量未提交的变更不断累积
- 提交信息如 "fix"、"update"、"misc"
- 格式更改与行为更改混在一起
- 项目中没有 `.gitignore`
- 提交了 `node_modules/`、`.env` 或构建产物
- 长期分支与主线严重偏离
- 对共享分支进行强制推送

## 验证

对于每个提交：

- [ ] 提交只做一件逻辑上独立的事情
- [ ] 提交信息解释了为什么，遵循类型约定
- [ ] 提交前测试通过
- [ ] 差异中不含机密信息
- [ ] 没有纯格式修改与行为修改混合
- [ ] `.gitignore` 涵盖了标准排除项

---
> Source: [peakdong68/my-agent-skills](https://github.com/peakdong68/my-agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
