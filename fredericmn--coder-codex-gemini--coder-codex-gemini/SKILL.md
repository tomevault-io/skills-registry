---
name: gemini-collaboration
description: | Use when this capability is needed.
metadata:
  author: FredericMN
---

# Gemini 协作流程

## 角色定位

**Gemini** 是与 Claude 同等级别的顶级 AI 专家（**按需调用**）：
- 🧠 **高阶顾问**：架构设计、技术选型、复杂方案讨论
- ⚖️ **独立审核**：代码 Review、方案评审、质量把关
- 🔨 **代码执行**：原型开发、功能实现（尤其擅长前端/UI）

## 触发场景

1. **用户明确要求**：用户指定使用 Gemini
2. **Claude 自主调用**：需要第二意见或独立视角时

## 工具参考

| 参数 | 默认值 | 说明 |
|------|--------|------|
| sandbox | workspace-write | 沙箱策略（灵活控制） |
| yolo | true | 跳过审批 |
| model | gemini-3-pro-preview | 默认模型 |
| max_retries | 1 | 自动重试 |

**会话复用**：保存 `SESSION_ID` 保持上下文。

## 独立决策

Gemini 的意见仅供参考。你（Claude）是最终决策者，需批判性思考，做出最优决策。

详细参数：[gemini-guide.md](gemini-guide.md)

---
> Source: [FredericMN/Coder-Codex-Gemini](https://github.com/FredericMN/Coder-Codex-Gemini) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
