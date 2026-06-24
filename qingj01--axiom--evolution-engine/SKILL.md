---
name: evolution-engine
description: Agent 自进化引擎 (v1.0)。整合知识收割、工作流优化、模式检测、反思引擎四大模块，赋予 Agent 自我学习和持续改进能力。 Use when this capability is needed.
metadata:
  author: qingj01
---

# Evolution Engine (自进化引擎)

本技能是 Agent 的"大脑升级系统"，负责从经验中学习、积累知识、识别模式、持续优化。

---

## 0. Core Philosophy (核心理念)

> "每一次任务都是学习机会，每一个错误都是改进素材。"

- **Continuous Learning**: 持续从对话和代码中提取知识
- **Pattern Recognition**: 识别可复用的代码和工作模式
- **Self-Reflection**: 定期反思，总结经验教训
- **Metric-Driven**: 用数据驱动优化决策

---

## 1. Modules (模块清单)

| Module | File | Trigger | Description |
|--------|------|---------|-------------|
| Knowledge Harvester | `evolution/knowledge_base.md` | 任务完成后 | 从对话中提取可复用知识 |
| Workflow Optimizer | `evolution/workflow_metrics.md` | 工作流完成后 | 追踪效能并提出优化建议 |
| Pattern Detector | `evolution/pattern_library.md` | 代码提交后 | 识别代码中的可复用模式 |
| Reflection Engine | `evolution/reflection_log.md` | 状态 → ARCHIVING | 自动反思并总结经验 |
| Learning Queue | `evolution/learning_queue.md` | 随时入队 | 管理待处理的学习素材 |

---

## 2. Commands (命令入口)

### 2.1 /evolve - 手动触发进化
**Trigger**: 用户输入 `/evolve` 或 "进化" / "学习"

**Action**:
1. 处理 `learning_queue.md` 中所有待处理素材
2. 更新 `knowledge_base.md` 和 `pattern_library.md`
3. 分析 `workflow_metrics.md` 生成优化建议
4. 输出进化报告

### 2.2 /reflect - 触发反思
**Trigger**: 用户输入 `/reflect` 或 "反思"

**Action**:
1. 读取当前会话的任务完成情况
2. 生成反思报告并追加到 `reflection_log.md`
3. 提取 Action Items

### 2.3 /knowledge - 查询知识库
**Trigger**: 用户输入 `/knowledge [query]` 或 "知识 [query]"

**Action**:
1. 搜索 `knowledge_base.md` 索引
2. 读取匹配的知识条目
3. 返回相关知识摘要

### 2.4 /patterns - 查询模式库
**Trigger**: 用户输入 `/patterns [query]` 或 "模式 [query]"

**Action**:
1. 搜索 `pattern_library.md`
2. 返回匹配的代码模式和模板

---

## 3. Automatic Behaviors (自动行为)

### 3.1 任务完成后入队
**Trigger**: 任何任务 (T-xxx) 标记为完成

**Action**:
```yaml
learning_queue.add:
  source_type: code_change
  source_id: T-xxx
  priority: P2
  metadata:
    files_changed: [...]
    description: "..."
```

### 3.2 错误修复后入队
**Trigger**: Auto-Fix 成功修复错误

**Action**:
```yaml
learning_queue.add:
  source_type: error_fix
  source_id: error-timestamp
  priority: P1
  metadata:
    error_type: "..."
    root_cause: "..."
    solution: "..."
```

### 3.3 工作流完成后记录指标
**Trigger**: 工作流执行完成（成功或失败）

**Action**:
1. 计算工作流耗时
2. 记录成功/失败状态
3. 追加到 `workflow_metrics.md`

### 3.4 IDLE 状态处理队列
**Trigger**: 状态变为 IDLE 且 `learning_queue` 不为空

**Action**:
1. 处理队列中 P0/P1 优先级素材
2. 更新知识库和模式库

---

## 4. Knowledge Harvester (知识收割机)

### 4.1 知识提取规则

从以下来源提取知识：

| Source | Extract If | Category |
|--------|------------|----------|
| 错误修复 | 新的错误类型或解决方案 | debugging |
| 架构决策 | 重大技术选型 | architecture |
| 代码模式 | 重复出现 3+ 次 | pattern |
| 工作流优化 | 显著效率提升 | workflow |
| 工具使用 | 新工具或新技巧 | tooling |

### 4.2 知识条目模板

```markdown
---
id: k-xxx
title: [Title]
category: [architecture|debugging|pattern|workflow|tooling]
tags: [tag1, tag2]
created: YYYY-MM-DD
confidence: 0.7
references: [source-id]
---

## Summary
[一句话总结]

## Details
[详细说明]

## Code Example (if applicable)
\`\`\`dart
// code
\`\`\`

## Related Knowledge
- k-yyy: [Related Title]
```

### 4.3 Confidence 更新规则

| Event | Confidence Change |
|-------|-------------------|
| 知识被再次验证 | +0.1 |
| 知识被引用使用 | +0.05 |
| 知识导致错误 | -0.2 |
| 30 天未使用 | -0.1 |

---

## 5. Pattern Detector (模式检测器)

### 5.1 模式识别触发

代码提交后，扫描 Git diff：
1. 检查是否与已知模式匹配
2. 检查是否出现新的重复结构

### 5.2 模式提升规则

```
IF pattern.occurrences >= 3 AND pattern.confidence >= 0.7
THEN promote to pattern_library as ACTIVE
```

### 5.3 复用提示

开发新功能时：
1. 读取功能描述
2. 搜索 `pattern_library.md`
3. 若有匹配模式，提示复用

---

## 6. Workflow Optimizer (工作流优化器)

### 6.1 指标收集点

| Workflow | Collect At |
|----------|-----------|
| feature-flow | 开始、PRD完成、每个任务完成、结束 |
| analyze-error | 开始、诊断完成、修复完成、结束 |
| start | 开始、上下文恢复完成、结束 |

### 6.2 优化建议触发

```
IF workflow.avg_duration > threshold * 1.5
OR workflow.success_rate < 0.8
THEN generate_optimization_suggestion()
```

### 6.3 建议模板

```markdown
## Optimization Suggestion: [Workflow Name]

**Issue**: [问题描述]
**Data**: 
- 平均耗时: X min (阈值: Y min)
- 成功率: X% (目标: 80%+)
- 最常见瓶颈: [Phase Name]

**Suggestion**: [优化建议]
**Expected Impact**: [预期效果]
```

---

## 7. Reflection Engine (反思引擎)

### 7.1 触发时机

- **自动**: 状态从 EXECUTING 变为 ARCHIVING
- **手动**: 用户输入 `/reflect`

### 7.2 反思流程

```
1. 读取 active_context.md 任务完成情况
2. 分析本次会话特点：
   - 任务完成率
   - 自动修复次数
   - 回滚次数
   - 耗时分布
3. 生成反思报告 (What Went Well / What Could Improve)
4. 提取 Learnings (转化为知识条目)
5. 提取 Action Items (转化为待办事项)
6. 追加到 reflection_log.md
```

### 7.3 Action Item 跟踪

Action Items 追加到 `active_context.md` 的任务队列中，标记为 `[REFLECTION]`：

```markdown
- [ ] [REFLECTION] 将 Loading Pattern 标准化为代码模板
```

---

## 8. Evolution Report (进化报告)

执行 `/evolve` 后，输出进化报告：

```markdown
# 🧬 Evolution Report - YYYY-MM-DD

## 📚 Knowledge Updated
- **New**: X items
- **Updated**: X items
- **Deprecated**: X items

## 🔄 Patterns Detected
- **New**: X patterns
- **Promoted**: X patterns

## 📊 Workflow Insights
- **Most Used**: [Workflow Name]
- **Bottleneck**: [Phase Name]
- **Optimization Suggestions**: X

## 💭 Reflections Processed
- **Sessions**: X
- **Action Items Completed**: X/Y

## 🎯 Recommended Next Steps
1. [Action 1]
2. [Action 2]
```

---

## 9. Integration Points (集成点)

### 9.1 与 context-manager 集成
- 反思结果写入 `active_context.md`
- 进化状态更新 frontmatter

### 9.2 与 feature-flow 集成
- 任务完成后触发知识入队
- 工作流完成后记录指标

### 9.3 与 analyze-error 集成
- 错误修复后触发知识入队
- 更新 `project_decisions.md` 的 Known Issues

### 9.4 与 GEMINI.md (全局配置) 集成
- 注册 `/evolve`, `/reflect`, `/knowledge`, `/patterns` 命令
- 添加自动行为触发器

---

## 10. Data Retention (数据保留)

| Data | Retention | Archive Policy |
|------|-----------|----------------|
| Knowledge Items | 永久 (Confidence > 0.5) | Confidence < 0.5 → deprecated → 7天后删除 |
| Workflow Metrics | 90 天详情，永久统计 | 90 天前详情归档 |
| Pattern Library | 永久 (Occurrences >= 3) | Occurrences < 3 → pending |
| Reflection Log | 永久摘要，30 天详情 | 30 天前详情归档 |
| Learning Queue | 处理后 7 天 | 7 天后删除 |

---

_Last Updated: 2026-02-08_

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/qingj01) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
