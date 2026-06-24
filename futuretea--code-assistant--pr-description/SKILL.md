---
name: code-pr-description
description: This skill should be used when the user asks to "generate PR description", "write PR", "PR description", "pull request description", "生成 PR 描述", "写 PR", "PR 描述", or discusses creating structured pull request descriptions based on code changes (PR 描述生成). Use when this capability is needed.
metadata:
  author: futuretea
---

# PR 描述生成

为代码变更生成标准化的 PR 描述，遵循 Problem/Solution/Test Plan 三段式格式。

## 直接操作（无需 Sub-Agent）

这些简单操作立即执行：

| 操作 | 命令 | 何时直接使用 |
|------|------|-------------|
| 查看变更文件 | `git diff --name-only master...HEAD` | 用户只想看改了哪些文件 |
| 查看变更统计 | `git diff --stat master...HEAD` | 用户只想看变更统计 |

## Sub-Agent 委托

### `code-pr-writer`
**用于**: 生成完整的 PR 描述

**何时委托**:
- 用户要求"生成 PR 描述"或"写 PR"
- 需要分析 diff 内容并生成结构化描述
- 需要确保格式和质量符合标准

**参数**:
```json
{
  "target_branch": "目标分支（默认 master 或 main）",
  "language": "zh" | "en"
}
```

## 决策树

```
用户请求：
├─ "看看改了什么" / "变更统计"
│  └─ 直接使用 git diff --stat
│
├─ "生成 PR 描述" / "写 PR"
│  └─ 委托给 code-pr-writer
│
└─ "更新 PR 描述" / "补充 Test Plan"
   └─ 委托给 code-pr-writer（基于现有描述补充）
```

## 工作流

### 步骤 1: 确认目标分支
```bash
# 检测默认分支
git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null || echo "main"
```

### 步骤 2: 启动 PR 生成 Agent
```
Task({
  subagent_type: "general-purpose",
  description: "生成 PR 描述",
  prompt: `你是 code-pr-writer。基于当前分支与 ${target} 的 diff，生成标准化的中文 PR 描述，遵循 Problem/Solution/Test Plan 格式。`
})
```

### 步骤 3: 展示 PR 描述

## 响应格式

```markdown
# Problem
描述解决的问题或需求背景(1-3 句话)

# Solution
说明实现方案和关键变更(3-8 点)

# Test Plan
列出详细的人工测试步骤(3-8 步)
```

## 错误处理

- **无变更**: 提示当前分支与目标分支无差异
- **目标分支不存在**: 列出可用分支供选择
- **变更过大**: 建议拆分 PR，先对核心变更生成描述

## 与其他技能的关系

| 场景 | 前置行动 | 使用技能 |
|------|----------|----------|
| rebase 后提 PR | 先分析 rebase | rebase-analysis |
| 重构后提 PR | 先简化代码 | code-simplification |
| 审查后提 PR | 先 Go 风格审查 | go-style-review |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/futuretea) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
