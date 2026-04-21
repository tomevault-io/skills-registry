---
name: code-simplification
description: This skill should be used when the user asks to "simplify code", "refactor", "clean up code", "optimize code", "reduce complexity", "简化代码", "重构", "代码优化", "清理代码", "减少复杂度", or discusses refactoring and cleaning up code without changing behavior (代码简化和优化). Use when this capability is needed.
metadata:
  author: futuretea
---

# 代码简化

简化和优化代码，提升清晰度、一致性和可维护性，同时保留所有功能。

## 直接操作（无需 Sub-Agent）

这些简单操作立即执行：

| 操作 | 何时直接使用 |
|------|-------------|
| 格式化代码 (`gofmt`) | 仅需格式化 |
| 移除未使用的导入 (`goimports`) | 仅需清理 import |
| 简单的变量重命名 | 单个文件内的小调整 |

## Sub-Agent 委托

### `code-optimizer`
**用于**: 系统化的代码简化和重构

**何时委托**:
- 用户要求"简化代码"或"重构"
- 需要分析多个文件的改进机会
- 涉及结构性调整（提取函数、合并逻辑等）
- 需要保证功能不变的前提下优化

**参数**:
```json
{
  "scope": "recent" | "file" | "directory",
  "path": "目标路径",
  "language": "go" | "typescript" | "python"
}
```

## 决策树

```
用户请求：
├─ "格式化代码" / "gofmt"
│  └─ 直接执行 gofmt / goimports
│
├─ "简化这个函数" / "refactor function X"
│  └─ 简单函数（< 30 行）→ 直接优化
│  └─ 复杂函数 → 委托给 code-optimizer
│
├─ "重构这个文件" / "简化代码"
│  └─ 委托给 code-optimizer
│
├─ "优化最近的修改"
│  └─ 委托给 code-optimizer（scope: recent）
│
└─ "清理整个目录"
   └─ 委托给 code-optimizer（scope: directory）
```

## 并行执行

### 多文件优化
```
用户: "简化 pkg/handler 和 pkg/service 目录的代码"
→ 并行启动：
  Agent 1: code-optimizer（pkg/handler）
  Agent 2: code-optimizer（pkg/service）
→ 汇总变更清单
```

## 工作流

### 步骤 1: 识别范围
- 最近修改？→ `git diff --name-only`
- 指定文件/目录？→ 直接使用
- 未指定？→ 默认最近修改的代码

### 步骤 2: 判断复杂度
- 简单调整（格式化、重命名）→ 直接操作
- 需要分析和结构调整 → 委托给 Sub-Agent

### 步骤 3: 启动优化 Agent
```
Task({
  subagent_type: "general-purpose",
  description: "优化代码 " + path,
  prompt: `你是 code-optimizer。对 ${path} 进行代码简化和优化，保持功能不变，提升清晰度和可维护性。`
})
```

### 步骤 4: 验证并展示变更

## 与其他技能的关系

| 场景 | 后续行动 | 使用技能 |
|------|----------|----------|
| 重构后审查风格 | Go 风格检查 | go-style-review |
| 重构后检查可读性 | 可读性评估 | readability-improvement |
| 重构完成提 PR | 生成 PR 描述 | pr-description |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/futuretea) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
