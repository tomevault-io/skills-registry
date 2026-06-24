---
name: architecture-design
description: 复杂系统架构建模与方法论工具。USE WHEN architecture, system design, software architecture, 架构设计, 系统设计, 架构建模, 架构分析, 架构评审, 架构决策, architecture review, architecture decision, system modeling, 形式化架构. 整合C4模型、形式化方法、ADR记录、第一性原理分析等能力。 Use when this capability is needed.
metadata:
  author: starlink-awaken
---

## Customization

**Before executing, check for user customizations at:**
`~/.claude/skills/PAI/USER/SKILLCUSTOMIZATIONS/architecture-design/`

If this directory exists, load and apply any PREFERENCES.md, configurations, or resources found there. These override default behavior. If the directory does not exist, proceed with skill defaults.

## 🚨 MANDATORY: Voice Notification (REQUIRED BEFORE ANY ACTION)

**You MUST send this notification BEFORE doing anything else when this skill is invoked.**

1. **Send voice notification**:
   ```bash
   curl -s -X POST http://localhost:8888/notify \
     -H "Content-Type: application/json" \
     -d '{"message": "Running architecture design workflow"}' \
     > /dev/null 2>&1 &
   ```

2. **Output text notification**:
   ```
   🔧 Running the **ArchitectureDesign** skill for [purpose]...
   ```

**This is not optional. Execute this curl command immediately upon skill invocation.**

# Architecture Design Skill

复杂系统架构建模与方法论工具，整合多种架构设计方法。

## 核心能力

| 能力 | 工具 | 适用场景 |
|------|------|----------|
| **概念建模** | 第一性原理分析 | 新系统从零设计 |
| **架构描述** | C4 Model | 系统全景可视化 |
| **形式化验证** | TLA+/Alloy | 关键逻辑正确性 |
| **决策记录** | ADR | 架构决策可追溯 |
| **架构评审** | 多角度分析 | 方案审查与改进 |

## Workflow Routing

Route to the appropriate workflow based on the request.

### 场景路由

| 场景 | 工作流 | 说明 |
|------|--------|------|
| 新系统设计 / 从零开始 | `Workflows/NewSystemDesign.md` | 第一性原理+能力建模 |
| 现有系统分析 | `Workflows/SystemAnalysis.md` | 架构理解+文档化 |
| 架构决策 | `Workflows/ArchitectureDecision.md` | 多方案评估+ADR |
| 架构评审 | `Workflows/ArchitectureReview.md` | 风险识别+改进建议 |
| 微服务拆分 | `Workflows/MicroserviceDesign.md` | 领域驱动拆分 |
| 架构演进 | `Workflows/ArchitectureEvolution.md` | 渐进式改进 |

### 方法路由

| 需求 | 调用技能 |
|------|----------|
| 追本溯源分析 | FirstPrinciples skill |
| C4图表绘制 | c4-modeling skill |
| 形式化建模 | formal-methods skill |
| ADR记录 | adr-management skill |
| 多角度评审 | Council skill |
| 对抗性分析 | RedTeam skill |

## 架构设计框架

### 第一性原理架构建模

```
┌─────────────────────────────────────────────────────────────┐
│  步骤1: 能力抽取                                          │
│  └── 系统需要哪些核心能力？                                  │
│       → 认知、记忆、决策、演化、身份                         │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  步骤2: 约束识别                                            │
│  └── 什么是真正的硬约束？                                   │
│       → 资源有限性、技术边界、哲学原则                        │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  步骤3: 架构构建                                            │
│  └── 从本质出发，重新组合子系统                              │
│       → 不预设形式，只解决问题                               │
└─────────────────────────────────────────────────────────────┘
```

### C4建模流程

```
Context (系统上下文) → Container (容器) → Component (组件) → Code (代码)
```

### 形式化验证流程

```
需求抽取 → TLA+/Alloy建模 → 模型检查 → 问题修复 → 验证通过
```

## 输出格式

### 架构设计文档模板

```markdown
# 架构设计: [系统名称]

## 1. 设计目标
- 核心目标: [是什么]
- 成功标准: [如何衡量]

## 2. 能力模型
| 能力 | 描述 | 优先级 |
|------|------|--------|
|      |      |        |

## 3. 约束分析
| 约束类型 | 约束描述 | 能否变更 |
|----------|----------|----------|
| 硬约束   |          | 否       |
| 软约束   |          | 是       |

## 4. 架构视图
### 4.1 C4 Context
[系统上下文图]

### 4.2 C4 Container
[容器图]

### 4.3 C4 Component
[组件图]

## 5. 关键决策
| ID | 决策 | 理由 | 替代方案 |
|----|------|------|----------|
|    |      |      |          |

## 6. 风险与缓解
| 风险 | 影响 | 概率 | 缓解措施 |
|------|------|------|----------|
|      |      |      |          |

## 7. 形式化验证
[如有] TLA+ Spec / Alloy Model 验证结果
```

## 集成模式

### 与其他Skills的协作

```markdown
## 架构设计流程

1. **需求分析**
   → 使用 FirstPrinciples 追本溯源

2. **方案生成**
   → 使用 BeCreative 发散思维
   → 使用 Council 多角度讨论

3. **方案评估**
   → 使用 RedTeam 对抗分析
   → 使用 c4-modeling 可视化

4. **决策记录**
   → 使用 adr-management 记录

5. **形式验证** (关键系统)
   → 使用 formal-methods 验证
```

## 适用场景示例

### 场景1: 设计AI数字生命系统
1. 使用 `NewSystemDesign` 工作流
2. 能力建模: 认知、记忆、决策、演化、身份
3. 约束分析: Token限制、时间约束、L0哲学
4. C4建模: 4层架构
5. ADR记录关键决策
6. TLA+验证Protocol状态机

### 场景2: 评审现有架构
1. 使用 `ArchitectureReview` 工作流
2. 分析现有架构文档
3. 识别风险点
4. 提出改进建议

### 场景3: 架构演进规划
1. 使用 `ArchitectureEvolution` 工作流
2. 分析当前架构
3. 规划演进路径
4. 制定迁移策略

## 质量标准

- 每个架构决策必须有ADR记录
- 关键逻辑必须经过形式化验证
- C4图必须保持一致性和可追溯性
- 架构文档必须版本化

---

**整合能力**: FirstPrinciples, c4-modeling, formal-methods, adr-management, Council, RedTeam

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/starlink-awaken) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
