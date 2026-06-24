---
name: code-go-style-review
description: This skill should be used when the user asks to "review Go style", "Go code review", "check Go conventions", "Go best practices", "Go 风格审查", "Go 代码审查", "检查 Go 规范", "Go 最佳实践", "golangci-lint", or discusses reviewing Go code against Uber Go Style Guide standards (Go 代码风格审查). Use when this capability is needed.
metadata:
  author: futuretea
---

# Go 代码风格审查

基于 Uber Go Style Guide 审查 Go 代码风格，涵盖惯用约定、性能优化和编程模式。

## 直接操作（无需 Sub-Agent）

这些简单操作立即执行：

| 操作 | 命令 | 何时直接使用 |
|------|------|-------------|
| 运行 linter | `golangci-lint run` | 用户只需要 lint 结果 |
| 格式化代码 | `goimports -w .` | 仅需格式化 |
| 运行 vet | `go vet ./...` | 仅需 vet 检查 |
| 竞态检测 | `go test -race ./...` | 仅需竞态检查 |

## Sub-Agent 委托

### `code-go-style-reviewer`
**用于**: 系统化的 Go 风格审查

**何时委托**:
- 用户要求"Go 风格审查"或"代码审查"
- 需要逐项检查接口、资源管理、错误处理等
- 需要按严重程度分类问题并提供修改建议
- 涉及多个文件或整个包的审查

**参数**:
```json
{
  "path": "目标文件或目录路径",
  "scope": "file" | "package" | "diff",
  "severity": "all" | "must-fix" | "should-fix"
}
```

### `code-readability-improver`（辅助）
**何时联合使用**:
- 风格审查 + 可读性改进 一起做
- 用户要求"全面代码审查"

## 决策树

```
用户请求：
├─ "跑一下 golangci-lint" / "lint 检查"
│  └─ 直接执行 golangci-lint run
│
├─ "审查这个文件的 Go 风格"
│  └─ 委托给 code-go-style-reviewer
│
├─ "审查最近的变更"
│  └─ 委托给 code-go-style-reviewer（scope: diff）
│
├─ "审查整个包"
│  └─ 委托给 code-go-style-reviewer（scope: package）
│
├─ "全面代码审查"
│  └─ 并行启动：
│     Agent 1: code-go-style-reviewer（风格审查）
│     Agent 2: code-readability-improver（可读性分析）
│  └─ 合并结果
│
└─ "只看 must-fix 的问题"
   └─ 委托给 code-go-style-reviewer（severity: must-fix）
```

## 并行执行

### 多包审查
```
用户: "审查 pkg/mcp 和 pkg/gateway 的 Go 风格"
→ 并行启动：
  Agent 1: code-go-style-reviewer（pkg/mcp）
  Agent 2: code-go-style-reviewer（pkg/gateway）
→ 汇总审查报告
```

### 全面审查
```
用户: "全面审查代码质量"
→ 并行启动：
  Agent 1: code-go-style-reviewer（风格和规范）
  Agent 2: code-readability-improver（结构和可读性）
→ 合并为统一的审查报告
```

## 工作流

### 步骤 1: 确定范围
- 运行 lint？→ 直接执行
- 文件/包/diff？→ 委托给 Sub-Agent
- 全面审查？→ 并行启动多个 Agent

### 步骤 2: 启动审查 Agent
```
Task({
  subagent_type: "general-purpose",
  description: "Go 风格审查 " + path,
  prompt: `你是 code-go-style-reviewer。基于 Uber Go Style Guide 审查 ${path} 的代码风格，检查接口使用、资源管理、错误处理、性能优化和代码规范，按严重程度分类问题并提供修改建议。`
})
```

### 步骤 3: 展示审查报告

## 审查范围说明

| 审查项 | 对应规则 | 示例 |
|--------|----------|------|
| 接口 | 无指针接口、编译时验证 | `var _ Handler = (*Server)(nil)` |
| 资源 | defer 关闭、边界拷贝 | `defer f.Close()` |
| 错误 | 一次处理、`%w` 包装 | `fmt.Errorf("...: %w", err)` |
| 性能 | strconv、预分配 | `make([]T, 0, n)` |
| 风格 | import 分组、early return | 标准库/第三方/本项目 |

## 与其他技能的关系

| 场景 | 后续行动 | 使用技能 |
|------|----------|----------|
| 发现可读性问题 | 改进可读性 | readability-improvement |
| 发现冗余代码 | 简化代码 | code-simplification |
| 审查完成提 PR | 生成 PR 描述 | pr-description |

## 错误处理

- **非 Go 文件**: 提示本技能仅适用于 Go 代码
- **golangci-lint 未安装**: 提示安装命令
- **编译失败**: 建议先修复编译错误再审查风格

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/futuretea) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
