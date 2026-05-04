---
name: codex-issue-plan-execute
description: Autonomous issue planning and execution workflow for Codex. Supports batch issue processing with integrated planning, queuing, and execution stages. Triggers on "codex-issue", "plan execute issue", "issue workflow". Use when this capability is needed.
metadata:
  author: neversight
---

# Codex Issue Plan-Execute Workflow

Streamlined autonomous workflow for Codex that integrates issue planning, queue management, and solution execution in a single stateful Skill. Supports batch processing with minimal queue overhead and dual-agent execution strategy.

## Architecture Overview

For complete architecture details, system diagrams, and design principles, see **[ARCHITECTURE.md](ARCHITECTURE.md)**.

Key concepts:
- **Persistent Dual-Agent System**: Two long-running agents (Planning + Execution) that maintain context across all tasks
- **Sequential Pipeline**: Issues → Planning Agent → Solutions → Execution Agent → Results
- **Unified Results**: All results accumulated in single `planning-results.json` and `execution-results.json` files
- **Efficient Communication**: Uses `send_input()` for task routing without agent recreation overhead

---

## ⚠️ Mandatory Prerequisites (强制前置条件)

> **⛔ 禁止跳过**: 在执行任何操作之前，**必须**阅读以下两份P0规范文档。未理解规范直接执行将导致输出质量不符合标准。

| Document | Purpose | When |
|----------|---------|------|
| [specs/issue-handling.md](specs/issue-handling.md) | Issue 处理规范和数据结构 | **执行前必读** |
| [specs/solution-schema.md](specs/solution-schema.md) | 解决方案数据结构和验证规则 | **执行前必读** |

---

## Execution Flow

### Phase 1: Initialize Persistent Agents
→ **查阅**: [ARCHITECTURE.md](ARCHITECTURE.md) - 系统架构  
→ **查阅**: [phases/orchestrator.md](phases/orchestrator.md) - 编排逻辑  
→ Spawn Planning Agent with `prompts/planning-agent.md` (stays alive)  
→ Spawn Execution Agent with `prompts/execution-agent.md` (stays alive)

### Phase 2: Planning Pipeline
→ **查阅**: [phases/actions/action-plan.md](phases/actions/action-plan.md), [specs/subagent-roles.md](specs/subagent-roles.md)
For each issue sequentially:
1. Send issue to Planning Agent via `send_input()` with planning request
2. Wait for Planning Agent to return solution JSON
3. Store result in unified `planning-results.json` array
4. Continue to next issue (agent stays alive)

### Phase 3: Execution Pipeline
→ **查阅**: [phases/actions/action-execute.md](phases/actions/action-execute.md), [specs/quality-standards.md](specs/quality-standards.md)
For each successful planning result sequentially:
1. Send solution to Execution Agent via `send_input()` with execution request
2. Wait for Execution Agent to complete implementation and testing
3. Store result in unified `execution-results.json` array
4. Continue to next solution (agent stays alive)

### Phase 4: Finalize
→ **查阅**: [phases/actions/action-complete.md](phases/actions/action-complete.md)
→ Close Planning Agent (after all issues planned)
→ Close Execution Agent (after all solutions executed)
→ Generate final report with statistics

### State Schema

```json
{
  "status": "pending|running|completed",
  "phase": "init|listing|planning|executing|complete",
  "issues": {
    "{issue_id}": {
      "id": "ISS-xxx",
      "status": "registered|planning|planned|executing|completed",
      "solution_id": "SOL-xxx-1",
      "planned_at": "ISO-8601",
      "executed_at": "ISO-8601"
    }
  },
  "queue": [
    {
      "item_id": "S-1",
      "issue_id": "ISS-xxx",
      "solution_id": "SOL-xxx-1",
      "status": "pending|executing|completed"
    }
  ],
  "context": {
    "work_dir": ".workflow/.scratchpad/...",
    "total_issues": 0,
    "completed_count": 0,
    "failed_count": 0
  },
  "errors": []
}
```

---

## Directory Setup

```javascript
const timestamp = new Date().toISOString().slice(0,19).replace(/[-:T]/g, '');
const workDir = `.workflow/.scratchpad/codex-issue-${timestamp}`;

Bash(`mkdir -p "${workDir}"`);
Bash(`mkdir -p "${workDir}/solutions"`);
Bash(`mkdir -p "${workDir}/snapshots"`);
```

## Output Structure

```
.workflow/.scratchpad/codex-issue-{timestamp}/
├── planning-results.json               # All planning results in single file
│   ├── phase: "planning"
│   ├── created_at: "ISO-8601"
│   └── results: [
│       { issue_id, solution_id, status, solution, planned_at }
│     ]
├── execution-results.json              # All execution results in single file
│   ├── phase: "execution"
│   ├── created_at: "ISO-8601"
│   └── results: [
│       { issue_id, solution_id, status, commit_hash, files_modified, executed_at }
│     ]
└── final-report.md                     # Summary statistics and report
```

---

## Reference Documents by Phase

### 🔧 Setup & Understanding (初始化阶段)
用于理解整个系统架构和执行流程

| Document | Purpose | Key Topics |
|----------|---------|-----------|
| [phases/orchestrator.md](phases/orchestrator.md) | 编排器核心逻辑 | 如何管理agents、pipeline流程、状态转换 |
| [phases/state-schema.md](phases/state-schema.md) | 状态结构定义 | 完整状态模型、验证规则、持久化 |
| [specs/agent-roles.md](specs/agent-roles.md) | Agent角色和职责定义 | Planning & Execution Agent详细说明 |

### 📋 Planning Phase (规划阶段)
执行Phase 2时查阅 - Planning逻辑和Issue处理

| Document | Purpose | When to Use |
|----------|---------|-------------|
| [phases/actions/action-plan.md](phases/actions/action-plan.md) | Planning流程详解 | 理解issue→solution转换逻辑 |
| [phases/actions/action-list.md](phases/actions/action-list.md) | Issue列表处理 | 学习issue加载和列举逻辑 |
| [specs/issue-handling.md](specs/issue-handling.md) | Issue数据规范 | 理解issue结构和验证规则 ✅ **必读** |
| [specs/solution-schema.md](specs/solution-schema.md) | 解决方案数据结构 | 了解solution JSON格式 ✅ **必读** |

### ⚙️ Execution Phase (执行阶段)
执行Phase 3时查阅 - 实现和验证逻辑

| Document | Purpose | When to Use |
|----------|---------|-------------|
| [phases/actions/action-execute.md](phases/actions/action-execute.md) | Execution流程详解 | 理解solution→implementation逻辑 |
| [specs/quality-standards.md](specs/quality-standards.md) | 质量标准和验收条件 | 检查implementation是否达标 |

### 🏁 Completion Phase (完成阶段)
执行Phase 4时查阅 - 收尾和报告逻辑

| Document | Purpose | When to Use |
|----------|---------|-------------|
| [phases/actions/action-complete.md](phases/actions/action-complete.md) | 完成流程 | 生成最终报告、统计信息 |

### 🔍 Debugging & Troubleshooting (问题排查)
遇到问题时查阅 - 快速定位和解决

| Issue | Solution Document |
|-------|------------------|
| 执行过程中状态异常 | [phases/state-schema.md](phases/state-schema.md) - 验证状态结构 |
| Planning Agent输出不符合预期 | [phases/actions/action-plan.md](phases/actions/action-plan.md) + [specs/solution-schema.md](specs/solution-schema.md) |
| Execution Agent实现失败 | [phases/actions/action-execute.md](phases/actions/action-execute.md) + [specs/quality-standards.md](specs/quality-standards.md) |
| Issue数据格式错误 | [specs/issue-handling.md](specs/issue-handling.md) |

### 📚 Architecture & Agent Definitions (架构和Agent定义)
核心设计文档

| Document | Purpose | Notes |
|----------|---------|-------|
| [ARCHITECTURE.md](ARCHITECTURE.md) | 系统架构和设计原则 | 启动前必读 |
| [specs/agent-roles.md](specs/agent-roles.md) | Agent角色定义 | Planning & Execution Agent详细职责 |
| [prompts/planning-agent.md](prompts/planning-agent.md) | Planning Agent统一提示词 | 用于初始化Planning Agent |
| [prompts/execution-agent.md](prompts/execution-agent.md) | Execution Agent统一提示词 | 用于初始化Execution Agent |

---

## Usage Examples

### Batch Process Specific Issues

```bash
codex -p "@.codex/prompts/codex-issue-plan-execute ISS-001,ISS-002,ISS-003"
```

### Interactive Selection

```bash
codex -p "@.codex/prompts/codex-issue-plan-execute"
# Then select issues from the list
```

### Resume from Snapshot

```bash
codex -p "@.codex/prompts/codex-issue-plan-execute --resume snapshot-path"
```

---

*Skill Version: 1.0*
*Execution Mode: Autonomous*
*Status: Ready for Customization*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
