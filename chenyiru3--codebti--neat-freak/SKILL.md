---
name: neat-freak
description: > Use when this capability is needed.
metadata:
  author: CHENyiru3
---

# 洁癖：知识库收尾同步

> **跨平台 Agent Skill**：Claude Code · OpenAI Codex · OpenCode · OpenClaw 通用。

使用本技能时，你是**知识库编辑**，不是记录员。记录员只会追加；编辑要审查全局、合并重复、修正过期、删除废弃。目标是让项目知识体系保持干净、准确、对新人友好，像有洁癖一样。

## 为什么重要

在 AI 协作开发里，代码可以重写，但**文档和记忆是跨会话、跨 agent 的桥梁**。记忆过期会让下一个 agent 基于错误前提行动；`docs/` 混乱会让同事、下游项目或未来接手的 AI 浪费时间。

这个技能的价值是：**让知识体系的每一层都跟上代码变化。**

## 三类知识，三种受众

先理解受众差异，否则很容易只改 `CLAUDE.md` / `AGENTS.md`，却漏掉真正给人类和下游系统看的文档。

| 位置 | 受众 | 职责 | 不同步的代价 |
|------|------|------|--------------|
| Agent 记忆系统（若平台支持） | Agent 自己跨会话复用 | 个人偏好、非显而易见的项目事实、跨项目 reference | 下次会话忘记历史决策 |
| 项目根 `CLAUDE.md` / `AGENTS.md` | 当前项目里的 AI | 项目约定、结构、红线、环境变量、路由清单 | 下次 AI 在项目里走弯路 |
| 项目 `docs/` + `README.md` | 人类同事、下游开发者、未来接手的 AI | 接入指南、架构说明、运维手册、交接说明、API 参考 | 他人或系统无法正确接入、运维或扩展 |

三层受众不同，职责不重叠。`CLAUDE.md` 写“新增了 device flow 五个路由”不等于 `docs/integration-guide.md` 说明“下游怎样接入这套 flow”。前者提醒 agent，后者教别人。两份都要对齐。

Agent 记忆系统路径因平台而异。速查见 [references/agent-paths.md](references/agent-paths.md)。如果当前 agent 没有独立记忆系统，跳过记忆层，把精力放在 `docs/` 和项目根 markdown。

## 执行流程

### 1. 盘点现状

**先做 `ls`，再做判断。不能跳过枚举。**

1. 列出 agent 记忆文件（如有）：
   - Claude Code：`ls ~/.claude/projects/<...>/memory/`，读取 `MEMORY.md` 及其引用的 `.md`
   - Codex / OpenCode / 其他：查对应位置，见 [references/agent-paths.md](references/agent-paths.md)
2. 对本次对话涉及的每个项目执行：
   - `ls <project-root>/`，确认根目录结构
   - `ls <project-root>/docs/ 2>/dev/null`，枚举所有 docs；没有也要确认
   - `find <project-root> -maxdepth 2 -name "*.md" -not -path "*/node_modules/*" -not -path "*/.git/*"`，兜底抓散落 markdown
   - 读取 `README.md`、`CLAUDE.md` / `AGENTS.md`、每一个 `docs/*.md`
3. 读取全局 agent 配置（若有），例如 `~/.claude/CLAUDE.md`、`~/.codex/AGENTS.md`
4. 回顾本次对话的全部相关事实

内部维护一张文件清单，对每个文件标记“已评估 / 要改 / 不用改”。不用把清单完整展示给用户，但不能漏文件。

### 2. 用变更影响矩阵识别波及面

不要只问“这次新增了什么事实”，要问“这个事实会影响哪些文档层级”。

常见映射：

- 新增 API / 路由：项目根 agent 文档路由清单 + integration guide + architecture 的 Routes
- 新增或改名环境变量：项目根 agent 文档环境变量表 + runbook + 下游 integration guide
- 新增数据库表：项目根 agent 文档 + architecture 的 Data Model
- 新增跨文件大功能：architecture 新章节 + integration guide + runbook + handoff / CHANGELOG
- 跨项目改动：上游和下游 docs 都要对齐
- 记忆层面：相对时间改为绝对日期；过期事实修正；重复条目合并；已完成待办删除

完整映射见 [references/sync-matrix.md](references/sync-matrix.md)。不确定时先查表。

重点检查这次对话是否跨项目。如果改了项目 A，而项目 B 通过 SDK、API、子域名、环境变量或共享协议依赖它，项目 B 的 docs 也要改。

### 3. 实际修改文件

必须真的修改文件：编辑现有 markdown，创建缺失文档，清理废弃文件。只描述“应该怎么改”不算完成。

推荐顺序：

1. 先改 `docs/` 和 `README.md`，因为它们面向外部读者。
2. 再改 `CLAUDE.md` / `AGENTS.md` 等项目内 agent 指南。
3. 最后整理 agent 记忆。

编辑原则：

- 合并优于追加：新信息更新旧条目，不重复记一条。
- 删除优于保留：完成的临时计划、被推翻的决策、过期上下文直接删。
- 精确优于冗长：一条记忆只讲清楚一件事。
- 使用绝对日期：写 `2026-04-29`，不写“今天”“最近”。
- 面向读者：`docs/` 的读者是第一次接触项目的人，默认对方只有 5 分钟。
- 受众不混：agent 指南不抄 docs 全文；docs 不写“我记得上次……”。

全局配置要极度克制。只有用户明确表达跨项目核心原则时，才更新 `~/.claude/CLAUDE.md`、`~/.codex/AGENTS.md` 等全局文件。项目细节不要进全局。

新增能力时，docs 通常要补四处：

1. integration guide 或外部视角文档：怎么用，包含 curl / SDK 示例 / 错误码。
2. architecture：怎么工作，包含数据流、状态机、设计取舍。
3. runbook：怎么运维，包含冒烟命令、故障排查、环境变量。
4. handoff 或 CHANGELOG：完成了什么。

API 速查表、环境变量表、术语表属于高频查询结构化信息，必须保持“所见即最新”。

### 4. 自检

修改完成后逐项过检查清单：

- [ ] 第一步列出的每个文件都已判断“已改”或“不用改”
- [ ] 记忆索引（若有）里的链接都指向存在的文件
- [ ] 每个记忆文件的 description 与内容一致
- [ ] 记忆之间没有互相矛盾
- [ ] `CLAUDE.md` / `AGENTS.md` 提到的路径、命令、工具、环境变量在代码中真实存在
- [ ] `README.md` 的安装和运行步骤与代码一致
- [ ] 新增 API 路由已出现在 integration guide 和 architecture
- [ ] 新增环境变量已出现在 runbook 和项目根 agent 文档
- [ ] 新增数据库表已出现在 architecture 的 Data Model 和项目根 agent 文档
- [ ] 跨项目影响已同步到下游项目 docs
- [ ] 没有相对时间遗留：`grep -E "今天|昨天|刚刚|最近|上周|today|yesterday|recently"` 应该清零，除非是在解释触发词或示例

哪条不能勾，就回去补。不要用“差不多”跳过。

### 5. 汇报

所有文件改完之后，用简洁摘要收尾：

```markdown
## 同步完成

### 记忆变更
- 更新：xxx（原因）
- 新增：xxx
- 删除：xxx（原因）

### 文档变更
- <项目 A>/CLAUDE.md — xxx
- <项目 A>/docs/integration-guide.md — xxx
- <项目 A>/docs/architecture.md — xxx
- <项目 B>/docs/<integration>.md — xxx

### 未处理
- xxx（原因，例如需要用户确认）
```

只列实际变更。没改的文件不要写进摘要。

## 特殊情况

**项目没有 README 或 agent 指南**：如果项目已经有可运行代码，就创建；如果还在早期探索阶段，可以跳过，但在摘要中说明。

**对话没有产生新事实**：仍然审查现有记忆和文档是否过期、冲突、使用相对时间。审查本身有价值。

**记忆之间存在无法自动判断的矛盾**：列入“未处理”，让用户决定。这是唯一需要用户介入的情况。

**跨项目改动**：每个涉及项目都完整跑一遍“盘点现状”。不要假设上游 docs 改了，下游就不用改。

**发现过去同步漏了东西**：直接补上。这个技能的职责就是持续维护知识库。

## 本项目本地优化

如果当前项目是 CodeBTI 或类似的 Markdown-first skill 仓库（包含 `SKILL.md`、`AGENT.md` / `AGENTS.md`、`MANIFEST.md`、`zh/`、`shared/`、`project/`、语言包目录、验证脚本等），先读 [references/codebti-markdown-workflow.md](references/codebti-markdown-workflow.md)，再执行上面的通用流程。

这个本地 overlay 的作用是把 neat-freak 的通用清理规则落到本仓库的真实维护面：manifest 漂移、中文镜像、示例/fixture、验证命令、已安装 skill 副本同步。

## 参考资料

- [references/sync-matrix.md](references/sync-matrix.md)：变更类型到文档位置的映射表
- [references/agent-paths.md](references/agent-paths.md)：各平台记忆与配置路径速查
- [references/codebti-markdown-workflow.md](references/codebti-markdown-workflow.md)：本仓库/Markdown skill 仓库的本地优化 overlay

---
> Source: [CHENyiru3/CodeBTI](https://github.com/CHENyiru3/CodeBTI) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
