---
name: code-rebase-analysis
description: This skill should be used when the user asks to "analyze rebase", "rebase risk", "rebase impact", "branch diverged", "conflict prediction", "分析 rebase", "rebase 风险", "分叉分支", "冲突预测", "rebase 前检查", or discusses evaluating the consequences of rebasing a diverged branch (rebase 影响分析). Use when this capability is needed.
metadata:
  author: futuretea
---

# Rebase 影响分析

分析分叉分支 rebase 的后果，在执行 rebase 前生成结构化分析报告。

## 直接操作（无需 Sub-Agent）

这些简单操作立即执行：

| 操作 | 命令 | 何时直接使用 |
|------|------|-------------|
| 查看分叉状态 | `git rev-list --count --left-right <local>...<target>` | 用户只想看 ahead/behind 数 |
| 查看分支列表 | `git branch -vv` | 用户只想查看分支信息 |
| 快进合并 | `git rebase <target>` | ahead=0，只有 behind（非分叉） |

## Sub-Agent 委托

### `code-rebase-analyzer`
**用于**: 分叉分支的完整 rebase 影响分析

**何时委托**:
- 分支同时 ahead > 0 且 behind > 0（分叉状态）
- 用户要求"分析 rebase 风险"或"rebase 前检查"
- 需要冲突预测、功能影响评估、兼容性检查

**参数**:
```json
{
  "local_branch": "当前分支（自动检测）",
  "target_branch": "目标分支（默认 main）"
}
```

## 决策树

```
用户请求：
├─ "查看分支状态" / "ahead behind"
│  └─ 直接使用 git rev-list --count --left-right
│
├─ 只有 behind（ahead=0）
│  └─ 直接快进：git rebase <target>
│
├─ "分析 rebase" / "rebase 风险" / "rebase 影响"
│  └─ 先确认分叉状态
│     ├─ 非分叉 → 提示无需分析
│     └─ 分叉 → 委托给 code-rebase-analyzer
│
└─ "rebase 前检查" / "冲突预测"
   └─ 委托给 code-rebase-analyzer（完整 6 阶段分析）
```

## 并行执行

### 多分支分析
```
用户: "分析 feature-a 和 feature-b 分支的 rebase 风险"
→ 并行启动：
  Agent 1: code-rebase-analyzer（feature-a → main）
  Agent 2: code-rebase-analyzer（feature-b → main）
→ 汇总对比展示
```

## 工作流

### 步骤 1: 确认分叉状态
```bash
git branch --show-current
git rev-list --count --left-right HEAD...<target>
```

### 步骤 2: 判断是否需要分析
- ahead > 0 且 behind > 0 → 需要分析
- 其他情况 → 直接操作或提示

### 步骤 3: 启动分析 Agent
```
Task({
  subagent_type: "general-purpose",
  description: "分析 rebase 影响",
  prompt: `你是 code-rebase-analyzer。分析当前分支 rebase 到 ${target} 的影响，包括分叉拓扑、冲突预测、功能影响、兼容性评估和安全执行策略。`
})
```

### 步骤 4: 展示分析报告

## 错误处理

- **非分叉状态**: 提示用户可以直接操作，无需分析
- **工作区不干净**: 提示先 `git stash` 或 `git commit`
- **目标分支不存在**: 列出可用分支供选择

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/futuretea) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
