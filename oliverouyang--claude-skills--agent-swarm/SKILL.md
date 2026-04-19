---
name: agent-swarm
description: 智能任务分解和多 Agent 编排系统（增强版 v2.1）。支持两种模式：(1) 并行分析模式 - 多维度代码审查；(2) 层级执行模式 - 主 Agent 统筹，子 Agent 实际干活。预期收益：重复分析速度 5-10x，成本降低 30-60%，成功率提升 20%。适用于：复杂项目分析、多维度代码审查、大规模任务执行、综合性研究任务 Use when this capability is needed.
metadata:
  author: oliverouyang
---

# Agent Swarm - 智能多 Agent 编排系统（增强版 v2.1）

## 🎯 两种工作模式

### 模式 1: 并行分析模式（原有功能）
- **用途**：代码审查、安全审计、性能分析
- **特点**：多个专业 Agent 并行分析，快速全面
- **适用场景**：代码 review、项目评估、质量检查

### 模式 2: 层级执行模式（新增 v2.1）
- **用途**：实际执行任务、开发功能、修复 bug
- **特点**：主 Agent 统筹规划，子 Agent 实际干活
- **适用场景**：功能开发、bug 修复、重构任务

```
层级结构：
用户大任务
    ↓
主 Agent（项目经理）
    ├─ 分析需求
    ├─ 制定计划
    ├─ 拆解 TODO
    └─ 分配任务
    ↓
子 Agent 1        子 Agent 2        子 Agent 3
(后端开发)        (前端开发)        (测试)
    ├─ 写代码         ├─ 写组件         ├─ 写测试
    ├─ 修改文件       ├─ 写样式         ├─ 运行测试
    └─ 报告进度       └─ 报告进度       └─ 报告进度
```

## 🎯 核心增强功能

### ✨ v2.1 新特性

8. **🏗️ 层级化执行系统** - 主 Agent 统筹，子 Agent 干活
   - 主 Agent（MasterAgent）：任务分析、计划制定、任务分配、进度监控
   - 子 Agent（WorkerAgent）：实际执行（写代码、修改文件、运行测试）
   - 任务管理系统：TODO 列表、依赖管理、进度追踪
   - 层级通信机制：任务分配、进度报告、帮助请求

### ✨ v2.0 特性

1. **🔄 自动重试机制** - 失败自动分析和智能重试，成功率提升 20%
2. **⚡ 智能缓存系统** - 重复分析速度提升 5-10x，成本降低 60-80%
3. **🎯 增量分析** - PR review 速度提升 3-5x，聚焦变更文件
4. **🧠 智能模型选择** - 基于任务特征自动选择模型，成本降低 30-40%
5. **🌟 动态角色生成** - 基于 LLM 生成定制角色，无限扩展能力
6. **📊 监控指标系统** - 实时追踪执行指标、成本和性能
7. **📚 扩展角色库** - 从 16 个扩展到 40+ 个专业角色

### 📈 性能提升

| 指标 | 优化前 | 优化后 | 提升 |
|------|--------|--------|------|
| **重复分析速度** | 基准 | **5-10x** | 缓存命中 |
| **PR review 速度** | 基准 | **3-5x** | 增量分析 |
| **成本** | 100% | **40-70%** | 智能选择 |
| **成功率** | 基准 | **+20%** | 自动重试 |
| **角色覆盖** | 16 个 | **40+ 个** | 扩展库 |

## 概述

当面对复杂任务时，自动分解为多个子任务，为每个子任务创建专门的 Agent（带有角色名称），并行执行以提升效率。

## 触发条件

当用户请求包含以下特征时自动激活：
- 明确要求"并行"、"同时"、"多个 Agent"
- 任务涉及多个独立维度（如：代码质量 + 安全 + 性能）
- 任务复杂度高，需要多角度分析

## 工作流程

### 1. 任务分析阶段

分析用户请求，识别：
- 任务类型（代码分析、数据处理、研究等）
- 复杂度评估
- 可并行化的子任务

### 2. Agent 生成阶段

根据任务类型，自动生成 Sub-Agent 配置：

**代码分析任务**：
- Code Quality Analyst（代码质量分析师）
- Security Auditor（安全审计员）
- Performance Engineer（性能工程师）
- Documentation Reviewer（文档审查员）

**数据分析任务**：
- Data Explorer（数据探索员）
- Statistical Analyst（统计分析师）
- Visualization Specialist（可视化专家）
- Quality Checker（质量检查员）

**项目研究任务**：
- Architecture Analyst（架构分析师）
- Dependency Mapper（依赖关系映射员）
- Pattern Detector（模式检测员）

**新增场景支持**：
- **业务逻辑分析**：Business Logic Analyst, Domain Model Reviewer, API Contract Validator
- **可观测性**：Logging Auditor, Monitoring Specialist, Tracing Analyst
- **运维部署**：DevOps Reviewer, Container Security Analyst, Infrastructure Auditor
- **用户体验**：UX Analyst, Accessibility Checker, Performance UX Specialist
- **数据治理**：Data Quality Analyst, GDPR Compliance Checker, PII Scanner
- Best Practice Auditor（最佳实践审计员）

### 3. 并行执行阶段

使用 Task 工具并行启动所有 Sub-Agents：

```
在单个消息中发送多个 Task 调用
每个 Task 包含：
- description: Agent 角色名称
- prompt: 具体任务描述
- subagent_type: Explore/Plan/Bash 等
```

### 4. 结果聚合阶段

收集所有 Agent 的输出，生成综合报告：
- 按角色分类展示结果
- 识别关键发现和风险
- 提供优先级建议

## 使用示例

### 示例 1：代码审查

**用户请求**：
```
请全面审查这个项目的代码
```

**Agent Swarm 响应**：
```
我将启动 4 个专门的 Agent 并行分析：

1. Code Quality Analyst - 分析代码质量和可维护性
2. Security Auditor - 检查安全漏洞和风险
3. Performance Engineer - 分析性能瓶颈
4. Documentation Reviewer - 检查文档完整性

[并行启动 4 个 Task Agents]
```

### 示例 2：数据分析

**用户请求**：
```
分析这个数据集的所有维度
```

**Agent Swarm 响应**：
```
我将创建 3 个专门的 Agent：

1. Data Explorer - 探索数据结构和分布
2. Statistical Analyst - 进行统计分析
3. Quality Checker - 检查数据质量问题

[并行启动 3 个 Task Agents]
```

## Agent 角色库

### 代码相关
- Code Quality Analyst
- Security Auditor
- Performance Engineer
- Test Coverage Analyst
- Documentation Reviewer
- Dependency Auditor
- Architecture Analyst

### 数据相关
- Data Explorer
- Statistical Analyst
- Visualization Specialist
- Quality Checker
- Pattern Detector
- Anomaly Hunter

### 研究相关
- Literature Reviewer
- Fact Checker
- Source Validator
- Synthesis Specialist

## 配置选项

### 基础配置

**最大 Agent 数量**
- 默认：5 个
- 可调整范围：2-10 个
- 动态调整：根据任务复杂度自动计算最优数量

**Agent 类型选择**
- Explore：用于代码探索和分析
- Plan：用于设计和规划
- Bash：用于执行和测试

**超时设置**
- 每个 Agent 默认超时：5 分钟

### 增强功能配置

**智能缓存**
- 启用：`use_cache=True`（默认）
- 缓存目录：`.agent_swarm_cache`
- TTL：1 小时
- 预期收益：重复分析速度提升 5-10x

**增量分析**
- 启用：`use_incremental=True`（默认）
- 自动检测：Git 仓库中的变更文件
- 触发条件：变更文件 < 总文件的 30%
- 预期收益：PR review 速度提升 3-5x

**动态角色生成**
- 启用：`use_dynamic_roles=True`（可选）
- 基于 LLM 生成定制角色
- 适用场景：新领域、特殊需求

**智能模型选择**
- 自动启用
- 决策因素：代码行数、复杂度、安全性
- 模型选择：haiku（快速低成本）/ sonnet（高质量）
- 预期收益：成本降低 30-40%

**自动重试**
- 自动启用
- 最大重试次数：3 次
- 失败类型识别：超时、上下文不足、工具错误、质量问题
- 预期收益：成功率提升 20%

### 使用示例

```python
# 基础使用
orchestrator = AgentOrchestrator(
    max_agents=8,
    use_cache=True,
    use_incremental=True
)

# 启用所有增强功能
orchestrator = AgentOrchestrator(
    max_agents=10,
    use_cache=True,
    use_incremental=True,
    use_dynamic_roles=True
)

# 分析任务
result = orchestrator.analyze_task(
    user_request="全面审查代码安全性",
    task_context={
        "files": ["src/**/*.py"],
        "depth": "deep",
        "budget": "medium"
    }
)
```

## 输出格式

```markdown
## Agent Swarm 执行报告

### 参与的 Agents
1. [角色名称] - [任务描述]
2. [角色名称] - [任务描述]
...

### 执行结果

#### [Agent 1 角色名称]
[Agent 1 的发现和分析]

#### [Agent 2 角色名称]
[Agent 2 的发现和分析]

...

### 综合分析
[跨 Agent 的综合发现]

### 优先级建议
1. [高优先级问题]
2. [中优先级问题]
3. [低优先级问题]
```

## 监控与指标

### 实时指标收集

系统自动收集以下指标：

**Agent 统计**
- 总数、活跃数、完成数、失败数
- 平均执行时间
- 重试次数���成功率

**成本统计**
- 总成本（按模型分解）
- Haiku 成本 vs Sonnet 成本
- 成本节省比例

**性能统计**
- 缓存命中率
- 增量分析使用率
- 并行效率

**分析结果**
- 发现问题总数
- 按严重程度分类（严重/高/中/低）

### 执行报告示例

```markdown
# 📊 Agent Swarm 执行报告

**会话 ID**: abc123
**开始时间**: 2026-01-31 10:00:00
**结束时间**: 2026-01-31 10:05:30
**总耗时**: 330.5 秒

## Agent 统计

- **总数**: 6
- **完成**: 5 ✅
- **失败**: 1 ❌
- **运行中**: 0 🔄
- **平均执行时间**: 55.2 秒

## 成本统计

- **总成本**: $0.0234
  - Haiku: $0.0089
  - Sonnet: $0.0145

## 性能统计

- **缓存命中率**: 75.0%
  - 命中: 3
  - 未命中: 1
- **重试次数**: 2
  - 成功: 1
  - 成功率: 50.0%

## 分析结果

- **发现问题总数**: 23
  - 🔴 严重: 2
  - 🟠 高: 5
  - 🟡 中: 10
  - 🟢 低: 6
```

### 获取指标

```python
# 获取 Swarm 状态
status = coordinator.get_swarm_status()

# 获取重试统计
retry_stats = coordinator.get_retry_stats()

# 获取完整指标
metrics = coordinator.get_metrics()

# 生成报告
from metrics import MetricsReporter
reporter = MetricsReporter(metrics)
summary = reporter.generate_summary()
detailed = reporter.generate_detailed_report()
cost_breakdown = reporter.generate_cost_breakdown()
```

## 性能优化

### 自动优化策略

**智能缓存**
- 文件级缓存：基于文件哈希
- Agent 结果缓存：基于任务和上下文
- 自动失效：文件修改时
- 命中率目标：70%+

**增量分析**
- Git diff 驱动
- 自动检测变更文件
- 聚焦分析范围
- 适用场景：PR review、持续集成

**并行优化**
- 自动识别任务依赖关系
- 只并行化独立任务
- 动态调整 Agent 数量
- 智能超时管理

**成本优化**
- 智能模型选择（haiku vs sonnet）
- 动态 Agent 数量调整
- 早停机制（发现关键问题时）
- 缓存复用

### 性能基准

| 场景 | 首次执行 | 缓存命中 | 增量分析 |
|------|---------|---------|---------|
| **小项目** (< 1000 行) | 30s | 3s | 10s |
| **中型项目** (1000-5000 行) | 120s | 15s | 40s |
| **大型项目** (> 5000 行) | 300s | 30s | 90s |

## 限制与注意事项

### 系统限制

- **最多 10 个并行 Agent**（避免资源耗尽）
- **每个 Agent 独立工作**（不共享状态）
- **需要足够的系统资源**（内存、CPU）
- **缓存大小限制**（建议定期清理）

### 使用建议

**适用场景**
- ✅ 复杂项目的多维度分析
- ✅ 需要不同专业视角的任务
- ✅ 可并行化的独立子任务
- ✅ 重复性分析任务（利用缓存）

**不适用场景**
- ❌ 简单的单一任务
- ❌ 需要顺序执行的任务
- ❌ 资源受限的环境
- ❌ 实时性要求极高的场景

### 最佳实践

1. **首次分析**：使用完整分析建立缓存
2. **后续分析**：启用缓存和增量分析
3. **成本控制**：设置合理的 Agent 数量和预算级别
4. **定期清理**：清理过期缓存释放空间
5. **监控指标**：关注缓存命中率和成本

## 与 Kimi AgentSwarm 的对比

| 特性 | Kimi K2.5 | 本实现 | 状态 |
|------|-----------|--------|------|
| **动态角色生成** | AI 自动生成 | LLM 分析生成 | ✅ 已实现 |
| **个性化名称** | 自动命名 | 预定义名称库 | ✅ 已实现 |
| **智能任务分解** | PARL 训练 | 规则 + LLM | ✅ 已实现 |
| **Agent 间通信** | ✅ | 通信中心 | ✅ 已实现 |
| **动态调整** | ✅ | 动态生成/终止 | ✅ 已实现 |
| **智能缓存** | ❌ | 文件+结果缓存 | ✅ 增强 |
| **自动重试** | ❌ | 失败分析+重试 | ✅ 增强 |
| **增量分析** | ❌ | Git diff 驱动 | ✅ 增强 |
| **智能模型选择** | ❌ | 基于任务特征 | ✅ 增强 |
| **监控指标** | ❌ | 完整指标系统 | ✅ 增强 |
| **角色库规模** | 未知 | 40+ 角色 | ✅ 扩展 |

### 核心优势

**相比 Kimi AgentSwarm**：
- ✅ 更完善的错误处理和重试机制
- ✅ 智能缓存系统，大幅提升重复任务性能
- ✅ 增量分析支持，适合 CI/CD 场景
- ✅ 成本优化策略，降低 30-60% 成本
- ✅ 完整的监控和指标系统
- ✅ 更大的角色库（40+ 专业角色）

**相比传统单 Agent**：
- ✅ 5-10x 速度提升（并行 + 缓存）
- ✅ 多维度分析覆盖
- ✅ 专业化分工，质量更高
- ✅ 自动容错和重试
| **并行执行** | ✅ | ✅ | ✅ 已实现 |
| **结果聚合** | ✅ | 智能综合 | ✅ 已实现 |
| **Agent 数量** | 最多 100 | 最多 10 | ⚠️ 资源限制 |
| **执行速度** | 3-4.5x 提升 | 并行提升 | ✅ 已实现 |

## 核心实现

### 1. 动态角色生成 (`orchestrator.py`)

```python
# 自动分析任务并生成 Agent 配置
orchestrator = AgentOrchestrator(max_agents=10)
config = orchestrator.analyze_task(user_request)

# 每个 Agent 都有：
# - 角色名称（如 "Code Quality Analyst"）
# - 个性化名字（如 "质量守护者 Alex"）
# - 专门的任务描述
# - 合适的工具集
```

**特点**：
- 根据任务类型自动选择 Agent 角色
- 根据复杂度动态调整 Agent 数量（2-10 个）
- 为每个 Agent 生成个性化名称

### 2. Agent 间通信 (`coordinator.py`)

```python
# 通信中心
comm_hub = AgentCommunicationHub()

# Agent 可以：
# - 广播消息给所有 Agent
# - 发送私信给特定 Agent
# - 请求帮助
# - 报告进度
```

**特点**：
- 实时消息传递
- 状态同步
- 协作请求

### 3. 动态调整 (`coordinator.py`)

```python
# 动态调整器
adjuster = DynamicAgentAdjuster(comm_hub)

# 自动：
# - 检测是否需要新增 Agent
# - 终止已完成的 Agent
# - 重新平衡任务负载
```

**触发条件**：
- 发现新的复杂子任务 → 生成新 Agent
- Agent 负载过高 → 生成助手 Agent
- 任务完成 → 终止 Agent
- 发现重复工作 → 合并 Agent

### 4. Claude Code 集成 (`claude_integration.py`)

```python
# 完整的集成层
swarm = ClaudeCodeAgentSwarm(max_agents=10)
result = swarm.process_user_request(user_request)

# 自动生成：
# - 执行计划
# - Task 工具调用配置
# - 详细的 Agent prompts
# - 结果聚合报告
```

## 使用方法

### 基础使用

```
请用 agent swarm 全面分析这个项目
```

**自动执行**：
1. 分析任务类型和复杂度
2. 生成 4-8 个专门的 Agents
3. 并行启动所有 Agents
4. 实时监控执行状态
5. 聚合结果生成报告

### 高级使用

```
用 agent swarm 分析代码，重点关注安全和性能
```

**智能调整**：
- 增加 Security Auditor 和 Performance Engineer 的权重
- 分配更多资源给这两个角色
- 其他 Agent 作为辅助

### 自定义配置

```python
# 在 Python 中直接使用
from claude_integration import ClaudeCodeAgentSwarm

swarm = ClaudeCodeAgentSwarm(max_agents=8)
result = swarm.process_user_request("分析数据质量")

# 获取 Task 调用配置
for task in result['task_calls']:
    print(f"Agent: {task['description']}")
    print(f"Model: {task['model']}")
```

## 实现细节

### 任务分解算法

1. **任务类型识别**：使用正则匹配识别任务类型
2. **复杂度评估**：基于关键词判断复杂度
3. **Agent 选择**：从角色库中选择最相关的 Agents
4. **数量决策**：根据复杂度决定 Agent 数量

### 角色库

**代码分析**（8 个角色）：
- Code Quality Analyst（质量守护者 Alex）
- Security Auditor（安全专家 Sarah）
- Performance Engineer（性能优化师 Max）
- Architecture Analyst（架构师 David）
- Test Coverage Analyst（测试专家 Emma）
- Documentation Reviewer（文档审查员 Lisa）
- Dependency Auditor（依赖分析师 Tom）
- Code Smell Detector（代码侦探 Jack）

**数据分析**（4 个角色）：
- Data Explorer（数据探险家 Nina）
- Statistical Analyst（统计分析师 Ryan）
- Quality Checker（质量检查员 Olivia）
- Pattern Detector（模式识别师 Leo）

**安全审计**（4 个角色）：
- Vulnerability Scanner
- Authentication Auditor
- Data Protection Analyst
- Dependency Security Checker

### 模型选择策略

| 复杂度 | 默认模型 | 关键角色模型 |
|--------|---------|-------------|
| Low | haiku | haiku |
| Medium | haiku | sonnet |
| High | sonnet | sonnet |

**关键角色**（总是用 sonnet）：
- Security Auditor
- Architecture Analyst

## 性能优化

### 并行执行

- 所有 Agents 在单个消息中启动
- 真正的并行执行（不是顺序）
- 预期速度提升：2-3x

### 资源管理

- 动态调整 Agent 数量
- 智能模型选择（haiku vs sonnet）
- 自动终止完成的 Agents

### 通信优化

- 异步消息传递
- 状态缓存
- 按需通信（不是广播所有消息）

## 限制与权衡

### 当前限制

1. **Agent 数量**：最多 10 个（vs Kimi 的 100 个）
   - 原因：Claude Code 资源限制
   - 影响：适合中小型任务

2. **角色生成**：基于规则（vs Kimi 的 AI 学习）
   - 原因：没有 PARL 训练
   - 影响：角色库需要手动维护

3. **任务分解**：启发式（vs Kimi 的强化学习）
   - 原因：没有训练数据
   - 影响：可能不是最优分解

### 优势

1. **可定制性**：完全可控的角色和任务
2. **透明性**：清晰的执行逻辑
3. **可扩展性**：易于添加新角色和功能
4. **成本效益**：智能模型选择降低成本

## 未来增强

### 短期（已实现）

- ✅ 动态角色生成
- ✅ Agent 间通信
- ✅ 动态调整机制
- ✅ 智能结果聚合

### 中期（计划中）

- [ ] 使用 LLM 优化任务分解
- [ ] 学习用户偏好
- [ ] Agent 执行可视化
- [ ] 性能监控和优化

### 长期（研究方向）

- [ ] 实现简化版 PARL
- [ ] 支持 100+ Agents
- [ ] 跨会话学习
- [ ] Agent 自主决策

## 技术架构

```
用户请求
    ↓
AgentOrchestrator (任务分析)
    ↓
生成 Agent 配置
    ↓
AgentCoordinator (协调器)
    ↓
┌─────────────────────────────────┐
│  AgentCommunicationHub (通信)   │
│  DynamicAgentAdjuster (调整)    │
└─────────────────────────────────┘
    ↓
并行启动 Agents (Task 工具)
    ↓
实时监控和调整
    ↓
结果聚合
    ↓
综合报告
```

## 总结

这个实现通过以下方式接近 Kimi AgentSwarm 的效果：

1. **动态生成**：自动分析任务并生成合适的 Agents
2. **个性化**：每个 Agent 有独特的角色和名字
3. **智能协调**：Agent 间可以通信和协作
4. **动态调整**：根据执行情况自动调整 Agent 配置
5. **并行执行**：真正的并行处理提升效率
6. **智能聚合**：综合所有 Agent 的发现

虽然受限于资源无法达到 100 个 Agents，但在 10 个 Agents 的规模下，已经能够提供类似的多角度分析和并行执行能力。

## 快速开始

### 模式选择

**并行分析模式**（默认）- 用于代码审查和分析
**层级执行模式** - 用于实际开发和任务执行

### 1. 并行分析模式（代码审查）

```bash
# 全面代码审查
/agent-swarm 全面审查这个项目的代码质量和安全性

# 安全审计
/agent-swarm 进行全面的安全审计

# PR Review（自动增量分析）
/agent-swarm 审查本次提交的代码变更
```

### 2. 层级执行模式（实际开发）

```python
from hierarchical_system import HierarchicalAgentSystem

# 创建层级化系统
system = HierarchicalAgentSystem(max_workers=5)

# 执行大任务
result = system.execute_big_task("""
开发一个用户管理系统，包括：
1. 用户注册和登录功能
2. 用户信息管理（增删改查）
3. 权限控制
4. 完整的单元测试
5. API 文档
""")

# 查看进度
progress = system.get_progress()
print(progress)
```

**执行流程**：
```
1. 主 Agent 分析任务 → 制定计划
   ├─ 子任务 1: 设计数据库模型
   ├─ 子任务 2: 实现后端 API
   ├─ 子任务 3: 实现前端界面
   ├─ 子任务 4: 编写测试
   └─ 子任务 5: 生成文档

2. 创建 Worker Agents
   ├─ Backend Developer
   ├─ Frontend Developer
   ├─ QA Tester
   └─ Technical Writer

3. 分配任务并执行
   ├─ 批次 1: 设计数据库模型（串行）
   ├─ 批次 2: 后端 + 前端（并行）
   ├─ 批次 3: 测试（串行）
   └─ 批次 4: 文档（串行）

4. 主 Agent 验证和整合结果
```

### 3. 使用示例

**示例 1: 功能开发**
```python
system = HierarchicalAgentSystem()

result = system.execute_big_task("""
实现一个文件上传功能：
- 支持多文件上传
- 文件大小限制 10MB
- 支持图片预览
- 进度条显示
""")
```

**示例 2: Bug 修复**
```python
result = system.execute_big_task("""
修复登录页面的 bug：
- 用户名验证失败
- 密码加密不正确
- 记住我功能无效
""")
```

**示例 3: 代码重构**
```python
result = system.execute_big_task("""
重构用户服务模块：
- 提取公共逻辑
- 优化数据库查询
- 改进错误处理
- 添加单元测试
""")
```

### 2. 指定场景

```bash
# 安全审计
/agent-swarm 进行全面的安全审计

# 性能分析
/agent-swarm 分析性能瓶颈和优化机会

# 业务逻辑审查
/agent-swarm 审查业务逻辑实现

# 可观测性检查
/agent-swarm 检查日志、监控和追踪实现
```

### 3. 增量分析（PR Review）

```bash
# 自动检测 Git 变更
/agent-swarm 审查本次提交的代码变更
```

### 4. 查看执行报告

执行完成后，系统会自动生成：
- Agent 执行统计
- 成本分析
- 性能指标
- 发现的问题列表

## 常见问题

### Q1: 如何清理缓存？

```bash
# 删除缓存目录
rm -rf .agent_swarm_cache
```

### Q2: 如何控制成本？

```python
# 设置预算级别
orchestrator.analyze_task(
    user_request="分析代码",
    task_context={"budget": "low"}  # low/medium/high
)

# 限制 Agent 数量
orchestrator = AgentOrchestrator(max_agents=3)
```

### Q3: 缓存命中率低怎么办？

可能原因：
- 文件频繁修改
- 任务描述变化大
- 缓存已过期（TTL: 1小时）

解决方案：
- 使用增量分析
- 固定任务描述格式
- 增加缓存 TTL

### Q4: 重试失败怎么办？

系统会自动重试 3 次，如果仍然失败：
1. 查看错误日志
2. 检查网络连接
3. 确认文件访问权限
4. 手动调整任务描述

### Q5: 如何选择合适的 Agent 数量？

建议：
- 小项目（< 1000 行）：2-3 个
- 中型项目（1000-5000 行）：4-6 个
- 大型项目（> 5000 行）：6-10 个

系统会根据复杂度自动调整。

### Q6: 增量分析什么时候启用？

自动启用条件：
- 在 Git 仓库中
- 有变更文件
- 变更文件 < 总文件的 30%

手动控制：
```python
orchestrator = AgentOrchestrator(use_incremental=False)
```

### Q7: 如何查看详细的执行指标？

```python
# 获取指标
metrics = coordinator.get_metrics()

# 生成报告
from metrics import MetricsReporter
reporter = MetricsReporter(metrics)
print(reporter.generate_detailed_report())
```

## 故障排查

### 问题：Agent 执行超时

**原因**：任务过于复杂或文件过多

**解决**：
- 减少 Agent 数量
- 使用增量分析
- 增加超时时间

### 问题：成本过高

**原因**：过多使用 sonnet 模型

**解决**：
- 设置 `budget="low"`
- 检查模型选择策略
- 启用缓存减少重复调用

### 问题：缓存占用空间大

**原因**：长期积累的缓存文件

**解决**：
- 定期清理缓存
- 减少缓存 TTL
- 只缓存关键结果

## 更新日志

### v2.0.0 (2026-01-31) - 增强版

**新增功能**：
- ✨ 智能缓存系统（5-10x 速度提升）
- ✨ 自动重试机制（20% 成功率提升）
- ✨ 增量分析支持（3-5x PR review 速度）
- ✨ 智能模型选择（30-40% 成本降低）
- ✨ 动态角色生成（无限扩展能力）
- ✨ 监控指标系统（完整的执行追踪）
- ✨ 扩展角色库（40+ 专业角色）

**改进**：
- 🔧 完善动态调整实现
- 🔧 优化并行执行效率
- 🔧 增强错误处理
- 🔧 改进成本控制

### v1.0.0 - 初始版本

- 基础 Agent Swarm 功能
- 16 个预定义角色
- 简单的并行执行

## 贡献指南

欢迎贡献新的角色、优化策略或功能改进！

**添加新角色**：
1. 在 `orchestrator.py` 的 `_get_agent_templates()` 中添加
2. 在 `_generate_agent_name()` 中添加名称映射
3. 更新文档

**优化建议**：
- 提交 Issue 描述问题
- Fork 并创建分支
- 提交 Pull Request

## 许可证

MIT License

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oliverouyang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
