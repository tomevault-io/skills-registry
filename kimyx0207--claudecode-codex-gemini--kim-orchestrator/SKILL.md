---
name: kim-orchestrator
description: | Use when this capability is needed.
metadata:
  author: kimyx0207
---

# AI多引擎编排技能

> **公众号：老金带你玩AI** | **微信：xun900207** | 备注AI加入AI交流群

## 概述

自动协调三个AI引擎完成软件开发工作流：
1. **Claude Code** - 需求分析和技术方案设计
2. **Codex CLI** - 代码生成
3. **Gemini CLI** - 代码审查

## 工作流程

### 阶段1：需求分析（Claude）

分析用户需求，输出技术方案JSON：

```json
{
  "task_description": "任务描述",
  "features": ["功能列表"],
  "tech_stack": {"language": "", "framework": "", "libraries": []},
  "file_structure": {"files": [{"path": "", "purpose": ""}]},
  "key_points": ["关键实现要点"],
  "risks": ["潜在风险"]
}
```

保存到：`.kim-orchestrator/phase1_requirements.json`

### 阶段2：代码生成（Codex）

调用Codex MCP Server生成代码：

```
使用 mcp__codex__codex 工具：
- prompt: 根据phase1的需求生成代码
- conversationId: "ai_team_<timestamp>"
```

保存到：`.kim-orchestrator/phase2_code.md`

### 阶段3：代码审查（Gemini）

调用Gemini MCP Server审查代码：

```
使用 mcp__gemini__gemini 工具：
- prompt: 审查phase2的代码
- reviewMode: true
```

保存到：`.kim-orchestrator/phase3_review.md`

## 配置

工作目录：`.kim-orchestrator/`

## 依赖

- Claude Code（必需）
- Codex CLI + MCP Server（可选，代码生成）
- Gemini CLI + MCP Server（可选，代码审查）

## 示例

```
# 简单任务
"实现用户登录功能"

# 复杂任务
"实现JWT登录，包含注册、登录、token刷新"

# 系统设计
"设计RBAC权限系统"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kimyx0207) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
