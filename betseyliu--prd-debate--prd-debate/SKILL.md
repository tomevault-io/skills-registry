---
name: debate
description: > Use when this capability is needed.
metadata:
  author: betseyliu
---

# 结构化辩论

通过跨模型对抗式辩论编排，产出高质量产品文档。

**三个角色**：
- **主持人（Host）**（你，主会话 Claude）— 设计框架、编排轮次、判断推进、执行回溯验证
- **提案者（Proposer）**（GPT-5.4，通过 Codex 调用）— 产出分析和提案
- **审查者（Reviewer）**（Claude 子代理）— 对抗式审查，发现弱点和缺口

## 流程总览

```
阶段 1：初始化
  → 检查已有辩论 → 创建工作区 → 初始化 .debate-state

阶段 2：意图澄清（强制）
  → 主持人结构化提问 → 用户确认意图 → 写入 intent-brief.md
  → 必须在任何框架设计或辩论开始之前完成

阶段 3：框架设计
  → 分析主题 → 设计 4-7 个讨论阶段 → 呈现给用户 → 写入 debate-framework.md

代码库调研（有条件触发）：
  → 主持人判断主题是否涉及现有代码库
  → 如涉及：Explore 代理扫描项目 → 写入 codebase-context.md
  → 目的：为产品设计提供现实基础，不产出技术方案

阶段 4：分阶段执行（每阶段）
  → 主持人决定执行模式：
    A) 探查轮？ → 提案者产出问题框架 → 审查者批评 → 主持人丰富关键问题
    B) 模块拆分？ → 拆分 → 逐模块单轮 → 整合多轮
    C) 标准：2-5 轮提案者↔审查者对抗式辩论
  → 推进时：写共识 + 核心承诺 + 回溯验证
  → 所有产出必须是产品视角 — 不涉及技术实现，不涉及技术排期

阶段 5：最终综合 → PRD（产品视角）+ 摘要 + 决策日志
阶段 6：产出 → 呈现结果 → 用户可审阅/修订
```

**自动推进**：每个阶段完成后，自动进入下一阶段。

## 工作区结构

```
debates/<debate-slug>/
  .debate-state               # YAML 检查点
  intent-brief.md             # 用户意图澄清（阶段 2 产出）
  debate-framework.md         # 阶段、目标、关键问题
  core-commitments.md         # 已确认承诺（回溯锚点）
  codebase-context.md         # 项目代码库摘要（如触发调研）
  phases/
    01-<phase-slug>/
      round-NN-proposer.md    # 提案者产出（00 = 探查轮）
      round-NN-reviewer.md    # 审查者产出
      consensus.md            # 阶段共识
      backtrack-check.md      # 回溯验证结果
      prompts/                # Codex 提示词文件（审计线索）
      modules/                # 子模块文件（如触发模块拆分）
        01-<module>/
          round-01-proposer.md / round-01-reviewer.md / mini-consensus.md
  output/
    prd.md / debate-summary.md / decision-log.md
```

---

## 阶段 1：初始化

1. 从 `/debate` 调用中解析用户的主题
2. 检查 `debates/` 中是否有进行中的辩论（恢复协议参见 `references/session-recovery.md`）：
   - 找到 → 读取 `.debate-state` → 提供恢复或新建选项
   - 未找到 → 继续创建
3. 生成 `debate-slug`（小写、连字符、最长 40 字符）
4. 创建工作区目录
5. 初始化 `.debate-state`：
   ```yaml
   version: "1.0"
   debate_name: "<描述性名称>"
   debate_slug: "<slug>"
   topic: "<用户原始主题，逐字记录>"
   created_at: "<ISO 时间戳>"
   current_phase: 0
   current_round: 0
   total_phases: 0
   status: "initializing"
   research_done: false
   intent_confirmed: false
   phase_statuses: {}
   last_action: "init"
   last_action_at: "<ISO 时间戳>"
   ```

---

## 阶段 2：意图澄清（强制）

**目标**：在开始任何框架设计或讨论之前，与用户进行深入的意图对齐，确保主持人完全理解用户的产品诉求。

**这是强制步骤，不可跳过。** 过早进入框架设计会导致方向偏差、返工和讨论资源浪费。澄清维度、提问模板和产出格式参见 `references/intent-clarification.md`。

### 执行方式

1. **首轮提问**：根据用户的初始描述，选择 3-5 个最关键的问题提出。不要一次性倾倒所有问题。
2. **追问与深入**：根据用户的回答，针对模糊或关键的点进行追问。
3. **信息汇总确认**：当主持人认为已有足够信息时，向用户呈现理解摘要，明确请求确认。
4. **用户确认后**：将澄清内容写入 `intent-brief.md`，更新 `.debate-state`（`intent_confirmed: true`），进入阶段 3。

---

## 阶段 3：框架设计

**目标**：为用户的主题设计定制讨论框架。

**不使用固定模板。** 设计框架时，读取 `references/framework-design.md` 获取基础模式和设计原则。

**重要**：框架必须从**产品视角**设计。每个阶段应聚焦产品层面的问题（用户价值、场景、体验、优先级、范围），不涉及技术实现细节。"用什么框架"、"接口怎么设计"、"数据库怎么建模"、"技术排期多久"等技术问题不属于此阶段。

1. 读取 `intent-brief.md` 作为框架设计的首要输入
2. 分析主题：类型、依赖链、可能的分歧领域
3. 设计 4-7 个阶段，每阶段包含：名称、目标、关键问题（3-5 个）、完成标准、依赖
4. **验证所有关键问题均为产品层面**：不涉及技术实现方案、不涉及技术排期
5. 写入 `debate-framework.md`
6. 呈现给用户确认
7. 用户确认后：更新 `.debate-state`，进入代码库调研判断

---

## 代码库调研（有条件触发）

**目标**：将辩论植根于项目的实际代码库，使 PRD 与现有产品能力兼容。

**重要**：代码库调研服务于**产品设计的现实基础**，而非技术规划。其目的是理解：
- 现有产品能力和用户体验（用户今天能做什么）
- 产品约束和边界（基于现有产品结构，什么可行/不可行）
- 产品模块之间的关系（功能之间如何关联）
- 数据和内容模型的产品含义（存在什么信息，如何组织）

代码库调研不产出技术实现方案、架构设计或开发排期。这些内容属于产品 PRD 定稿**之后**的独立技术方案阶段。

进入此阶段时，读取 `references/codebase-research.md` 获取完整协议、Explore 代理提示词和产出格式。

**触发条件**（满足任一）：主题涉及演进现有功能；关键问题涉及技术约束；用户提到了具体模块或组件。

**跳过条件**（满足任一）：纯策略/商业主题；全新产品；非有意义的代码仓库。

触发时：
1. Explore 代理扫描代码库（1-2 个代理，并行）
2. 主持人综合产出 `codebase-context.md`（1000-2000 字）
3. 上下文注入提案者/审查者提示词（参见 `references/context-management.md`）
4. 更新 `.debate-state`：`research_done: true`

---

## 阶段 4：分阶段执行

**这是核心辩论循环。** 对每个阶段：

**关键约束 — 仅限产品视角**：整个讨论过程的产出必须是产品方案，不是技术方案。讨论中可以引用代码库现状作为产品决策的依据（如"现有产品已支持 XX 能力"），但不应深入讨论技术实现细节（如"应该用 React 还是 Vue"、"数据库表怎么设计"、"接口怎么定义"）。技术实现方案和技术排期应在产品 PRD 完成后的独立技术方案阶段中进行。

### 步骤 1：确定执行模式

**探查轮？** 触发条件：早期阶段信息不足；关键问题需要深入定义；前序阶段缺乏上下文。跳过条件：后期阶段有丰富的前序承诺或评估型目标。

**模块拆分？** 触发条件：阶段涉及详细规格说明；识别出 3 个以上独立子模块；关键问题按模块分组。跳过条件：单一内聚主题。

两者可以共存：探查轮 → 模块拆分 → 整合轮。

### 步骤 2：执行轮次

**探查轮（第 0 轮）** — 触发时，读取 `references/proposer-protocol.md` 获取探查轮提示词模板：
1. 提案者产出问题框架（不是提案） → `round-00-proposer.md`
2. 审查者批评框架完整性 → `round-00-reviewer.md`
3. 主持人合并双方 → 更新 `debate-framework.md` 中的关键问题
4. 进入第 1 轮。不计入 5 轮上限。

**模块拆分** — 触发时，读取 `references/proposer-decomposition.md` 获取提案者模板，读取 `references/reviewer-decomposition.md` 获取审查者模板：
1. 拆分轮：提案者列出子模块 → 审查者验证 → 主持人确认
2. 逐模块轮（每模块 1 轮）：提案者提案 → 审查者批评 → 主持人写小共识
3. 整合轮（2-5 轮，标准推进）：提案者整合所有模块 → 审查者批评跨模块一致性

**标准轮（第 1-5 轮）** — 准备提示词时，读取 `references/proposer-protocol.md` 获取提案者模板，读取 `references/reviewer-protocol.md` 获取审查者模板：
1. 主持人准备提案者提示词（阶段目标 + 关键问题 + 上轮摘要 + 审查者异议 + 核心承诺）。**不包含完整辩论历史** — 参见 `references/context-management.md`。
2. 通过 Bash 执行 Codex CLI 调用提案者：
   ```bash
   codex exec -o "<output-path>" --full-auto --ephemeral - < "<prompt-file-path>"
   ```
   其中 `<prompt-file-path>` 是写好的 prompt 文件路径，`<output-path>` 是输出结果文件路径（如 `round-NN-proposer.md`）。
   `-` 表示从 stdin 读取 prompt，`--full-auto` 启用自动执行，`--ephemeral` 不持久化会话。
3. 验证产出：长度 ≥ 200 字、必要段落存在、关键问题已覆盖。检查失败则以明确的缺口描述重新提示（最多 2 次重试）。
4. 通过 Agent 工具调用审查者（model: opus）
5. 主持人判断收敛性 — 参见 `references/phase-progression.md` 获取 5 选 3 推进标准

### 步骤 3：阶段完成

当推进标准满足（或第 5 轮强制推进）时：
1. 写入 `consensus.md`（已达成共识的立场、已解决的分歧、权衡、开放项）
2. 提取核心承诺 → 追加到 `core-commitments.md`，编号为 `C{phase}.{index}`
3. 回溯验证 — 执行此检查时读取 `references/backtracking-algorithm.md`：
   - 3 个维度：对齐性、范围、优先级
   - 硬违规（矛盾） → 阻止推进，补充轮
   - 软违规 → 必须在共识中记录
4. 更新 `.debate-state`，自动推进到下一阶段

---

## 阶段 5：最终综合

所有阶段完成后：
1. 读取所有 `consensus.md` + `core-commitments.md` + `debate-framework.md` + `intent-brief.md`
2. 产出 `output/prd.md` — 按 `references/prd-template.md` 的结构组织内容
   - **产品视角**：PRD 描述的是"做什么"和"为什么做"，不是"怎么做"。不包含技术架构设计、接口定义、数据库方案、技术排期等内容。
   - **如涉及技术约束**：仅以产品约束的形式出现（如"需兼容现有 XX 功能"），不展开技术细节。
3. 产出 `output/debate-summary.md`：逐阶段摘要、统计数据、亮点
4. 产出 `output/decision-log.md`：按时间顺序的决策及理由
5. 更新 `.debate-state`：`status: "completed"`

---

## 阶段 6：产出

```
辩论完成："{debate_name}"
共 {total_phases} 个阶段，{total_rounds} 轮，{commitment_count} 条承诺

产出文件：
- debates/<slug>/output/prd.md
- debates/<slug>/output/debate-summary.md
- debates/<slug>/output/decision-log.md

A) 查看完整 PRD  B) 深入某个阶段  C) 修订某个章节
```

---

## 用户介入

用户可以在任意轮次间插话：

| 用户说 | 主持人操作 |
|---|---|
| "考虑一下 X" / "别忘了 Y" | 作为约束条件加入下轮提示词 |
| "方向不对" / "聚焦到 Z" | 调整阶段策略，修改关键问题 |
| "跳过这个阶段" | 标记为已跳过，推进 |
| "暂停" | 保存状态，等待 |
| "回到阶段 N" | 重置到该阶段 |
| "增加一个关于 X 的阶段" | 在框架中插入新阶段 |

处理后，**明确确认**变更内容再继续。

---

## 错误处理

**Codex 故障**：重试一次 → 如超时：减少 prompt 长度 → 如持续失败：回退到 Claude 子代理作为临时提案者（在记录中标注）。

**产出质量差**：以明确的缺口描述重新提示，最多 2 次重试。重试后仍不佳，继续推进并标记。

**失控辩论**：总轮次 > `total_phases × 4` → 提醒用户，提供压缩或结束选项。

---

## 禁忌

| 不要 | 原因 |
|-------|-----|
| 跳过意图澄清或草草了事 | 意图不对齐会导致辩论轮次浪费和 PRD 方向错误 |
| 对所有主题使用固定阶段模板 | 框架必须量身定制 |
| 在辩论中包含技术实现或技术排期 | 产品 PRD 聚焦于"做什么"和"为什么"，技术方案是独立的后续阶段 |
| 将完整辩论历史传给子代理 | 上下文溢出 — 使用有限制的摘要 |
| 让审查者看到提案者的提示词 | 会偏向附和 |
| 即使审查者同意也在 1 轮后推进 | 最少 2 轮确保稳健性 |
| 跳过回溯验证 | 核心机制 — 永不跳过 |
| 每轮后都向用户做总结 | 仅在阶段转换时 |
| 在辩论中注入主持人自己的观点 | 主持人负责编排，不参与辩论 |

## 核心原则

1. **意图优先** — 未经充分的意图澄清，不得开始框架设计。先理解"为什么"，再讨论"做什么"
2. **产品视角** — 整个辩论产出的是产品文档。不涉及技术实现细节、技术排期、架构设计。这些属于独立的下游阶段
3. **文件即状态** — 所有状态在文件中，不在对话记忆中。每个操作都更新 `.debate-state`
4. **代理上下文有界** — 每个代理只看到它需要的内容。参见 `references/context-management.md`
5. **天然对抗** — 审查者的职责是攻击，不是验证
6. **回溯不可妥协** — 每次阶段推进都检查前序承诺
7. **诚实收敛** — 带有文档化分歧的部分共识是合法的
8. **跨模型优势** — GPT-5.4 提案，Claude 审查。不同的盲点
9. **用户是最终权威** — 任何用户介入都优先于辩论动态
10. **代码库植根** — 当主题涉及现有项目时，PRD 必须与真实产品能力兼容（而非规定技术方案）

---
> Source: [betseyliu/prd-debate](https://github.com/betseyliu/prd-debate) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
