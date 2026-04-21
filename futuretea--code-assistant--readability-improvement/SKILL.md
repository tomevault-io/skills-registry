---
name: code-readability-improvement
description: This skill should be used when the user asks to "improve readability", "reduce abstraction", "inline functions", "simplify call depth", "提高可读性", "减少抽象", "内联函数", "降低调用深度", "代码可读性", or discusses refactoring code for better human readability (提高代码可读性). Use when this capability is needed.
metadata:
  author: futuretea
---

# 代码可读性改进

识别和消除影响代码清晰度的结构问题，优先保持局部性和上下文完整性。

## 直接操作（无需 Sub-Agent）

这些简单操作立即执行：

| 操作 | 何时直接使用 |
|------|-------------|
| 内联单个转发函数 | 用户指出具体函数需要内联 |
| 重命名单个变量/函数 | 明确的命名改进 |
| 添加注释 | 补充缺失的"为什么"注释 |

## Sub-Agent 委托

### `code-readability-improver`
**用于**: 系统化的可读性分析和改进

**何时委托**:
- 用户要求"提高可读性"或"减少抽象"
- 需要分析函数调用深度和局部性
- 需要识别命名问题、职责混乱等结构问题
- 涉及多个文件的可读性重构

**参数**:
```json
{
  "path": "目标文件或目录路径",
  "scope": "file" | "function" | "module",
  "function_name": "指定函数名（可选）"
}
```

### `code-go-style-reviewer`（辅助）
**何时联合使用**:
- 可读性改进 + 风格审查 一起做
- 用户要求"全面代码审查"

## 决策树

```
用户请求：
├─ "内联这个函数" / "去掉这层封装"
│  └─ 直接操作
│
├─ "这个文件可读性不好" / "调用层级太深"
│  └─ 委托给 code-readability-improver
│
├─ "分析这个模块的代码结构"
│  └─ 委托给 code-readability-improver
│
├─ "全面代码审查"
│  └─ 并行启动：
│     Agent 1: code-readability-improver（结构分析）
│     Agent 2: code-go-style-reviewer（风格审查）
│  └─ 合并结果
│
└─ "减少这个包的复杂度"
   └─ 委托给 code-readability-improver（scope: module）
```

## 并行执行

### 多文件/多模块分析
```
用户: "分析 pkg/handler 和 pkg/service 的可读性"
→ 并行启动：
  Agent 1: code-readability-improver（pkg/handler）
  Agent 2: code-readability-improver（pkg/service）
→ 汇总问题清单和改进建议
```

### 全面审查
```
用户: "全面审查 pkg/mcp 的代码质量"
→ 并行启动：
  Agent 1: code-readability-improver（结构和可读性）
  Agent 2: code-go-style-reviewer（风格和规范）
→ 合并为统一的审查报告
```

## 工作流

### 步骤 1: 确定范围和类型
- 单个函数？→ 判断是否直接操作
- 文件/模块？→ 委托给 Sub-Agent
- 全面审查？→ 并行启动多个 Agent

### 步骤 2: 启动可读性改进 Agent
```
Task({
  subagent_type: "general-purpose",
  description: "分析代码可读性 " + path,
  prompt: `你是 code-readability-improver。分析 ${path} 的代码可读性，检查函数调用深度、局部性、命名质量和职责内聚性，识别问题并应用重构模式改进。`
})
```

### 步骤 3: 展示分析结果和改进方案

## 与其他技能的关系

| 场景 | 后续行动 | 使用技能 |
|------|----------|----------|
| 可读性改进后简化 | 进一步简化 | code-simplification |
| 可读性改进后审查 | Go 风格检查 | go-style-review |
| 改进完成提 PR | 生成 PR 描述 | pr-description |

## 错误处理

- **非代码文件**: 建议使用 text-humanization
- **生成代码**: 提示自动生成的代码不建议手动重构

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/futuretea) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
