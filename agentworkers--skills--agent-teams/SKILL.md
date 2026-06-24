---
name: agent-teams
description: Coordinate Claude Code Agent Teams through filesystem-based protocol. Use Use when this capability is needed.
metadata:
  author: AgentWorkers
---

> **夜市技能** — 该技能源自 [claude-night-market/conjure](https://github.com/athola/claude-night-market/tree/master/plugins/conjure)。若要全面体验代理、钩子及命令的功能，请安装 Claude Code 插件。

## 目录

- [概述](#overview)
- [使用场景](#when-to-use)
- [先决条件](#prerequisites)
- [协议架构](#protocol-architecture)
- [快速入门](#quick-start)
- [协调工作流程](#coordination-workflow)
- [模块参考](#module-reference)
- [与 Conjure 的集成](#integration-with-conjure)
- [故障排除](#troubleshooting)
- [退出标准](#exit-criteria)

### 代理团队协调

## 概述

Claude Code 代理团队允许多个 Claude CLI 进程通过基于文件系统的协调协议进行协作。每个团队成员都在 tmux 窗口中运行独立的 `claude` 进程，并通过受 `fcntl` 锁保护的 JSON 文件进行通信。无需数据库、守护进程或网络层。

该技能提供了有效管理代理团队的模式。

## 使用场景

- 在多个文件或模块之间进行并行开发
- 多代理代码审查（一个代理负责审查，另一个代理负责实现修复）
- 需要在子系统间协调进行的大型重构
- 具有自然并行性的任务，可以从并发代理中受益

## 不适用场景

- 单个文件的修改或小型任务（开销大于收益）
- 需要严格顺序处理的任务（代理之间的协调较为松散）
- 当 `claude` CLI 未安装或 tmux 未安装时

## 先决条件

```bash
# Verify Claude Code CLI
claude --version

# Verify tmux (required for split-pane mode)
tmux -V

# Enable experimental feature (set by spawner automatically)
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

## 协议架构

```
~/.claude/
  teams/<team-name>/
    config.json            # Team metadata + member roster
    inboxes/
      <agent-name>.json    # Per-agent message queue
      .lock                # fcntl exclusive lock
  tasks/<team-name>/
    1.json ... N.json      # Auto-incrementing task files
    .lock                  # fcntl exclusive lock
```

**设计原则：**
- **文件系统作为数据库**：使用 JSON 文件，通过 `tempfile` 和 `os.replace` 实现原子写操作
- **`fcntl` 锁定**：防止对收件箱和任务的并发读写操作导致数据损坏
- **编号任务**：使用自动递增的 ID 和顺序文件命名
- **松耦合**：代理自行轮询收件箱；不使用推送通知

## 快速入门

### 1. 创建团队

```bash
# Programmatic team setup (via MCP or direct API)
# Team config written to ~/.claude/teams/<team-name>/config.json
```

团队配置包含：
- `name`（名称）、`description`（描述）、`created_at`（毫秒级时间戳）
- `lead_agent_id`（团队负责人 ID）、`lead_session_id`（团队负责人会话 ID）
- `members`（数组）——包含 `LeadMember` 和 `TeammateMember` 对象

### 2. 创建团队成员

每个团队成员都是一个独立的 `claude` CLI 进程，启动时会携带身份标志：

```bash
claude --agent-id "backend@my-team" \
       --agent-name "backend" \
       --team-name "my-team" \
       --agent-color "#FF6B6B" \
       --parent-session-id "$SESSION_ID" \
       --agent-type "general-purpose" \
       --model sonnet
```

有关 tmux 窗口管理和颜色分配的详细信息，请参阅 `modules/spawning-patterns.md`。

### 3. 创建带有依赖关系的任务

```json
{
  "id": "1",
  "subject": "Implement API endpoints",
  "description": "Create REST endpoints for user management",
  "status": "pending",
  "owner": null,
  "blocks": ["3"],
  "blocked_by": [],
  "metadata": {}
}
```

有关状态机和依赖关系管理的详细信息，请参阅 `modules/task-coordination.md`。

### 4. 通过消息进行协调

```json
{
  "from": "team-lead",
  "text": "API endpoints are ready for integration testing",
  "timestamp": "2026-02-07T22:00:00Z",
  "read": false,
  "summary": "API ready"
}
```

有关消息类型和收件箱操作的详细信息，请参阅 `modules/messaging-protocol.md`。

## 协调工作流程

1. **`agent-teams:team-created`** — 初始化团队配置和目录
2. **`agent-teams:teammates-spawned`** — 在 tmux 窗口中启动代理
3. **`agent-teams:tasks-assigned`** — 创建带有依赖关系的任务并分配负责人
4. **`agent-teams:coordination-active`** — 代理领取任务、交换消息并标记任务完成
5. **`agent-teams:team-shutdown`** — 通过审批协议实现优雅关闭

## 团队角色

每个团队成员都有一个角色，决定了他们的能力和任务兼容性。定义了五种角色：`implementer`（默认）、`researcher`（研究员）、`tester`（测试员）、`reviewer`（审核员）和 `architect`（架构师）。角色限制了代理可以处理的风险等级——有关完整的权限矩阵和角色-风险兼容性表，请参阅 `modules/crew-roles.md`。

## 团队组建

有关任务级别的团队规模建议，请参考 `references/team-formation.md`。其中定义了：
- **角色定义**：协调者（任务负责人）、代理（任务执行者）、审核员（提出挑战者）
- **团队规模建议**：简单（1人）、中等（2-4人）、复杂（5-7人）、关键（5-10人）
- **最大团队规模**：10个代理（协调开销限制）
- **文件所有权规则**：通过明确的所有权规则防止冲突

有关完整的团队规模建议和示例团队组建，请参阅 `references/team-formation.md`。

## 健康监控

可以通过心跳消息和任务有效期来监控团队成员的状态。团队负责人每 60 秒轮询一次团队健康状况，并使用两阶段检测机制（`health_check` 探测 + 30 秒等待）。如果代理停滞不前，其任务将被释放，并根据“替换而非等待”的原则重新分配给其他代理。有关完整协议和状态机的详细信息，请参阅 `modules/health-monitoring.md`。

## 模块参考

- **team-management.md**：团队生命周期、配置格式、成员管理
- **messaging-protocol.md**：消息类型、收件箱操作、锁定模式
- **task-coordination.md**：任务创建/读取/更新/删除（CRUD）、状态机、依赖关系检测
- **spawning-patterns.md**：tmux 窗口管理、CLI 标志、窗口管理
- **crew-roles.md**：角色分类、权限矩阵、角色-风险兼容性
- **health-monitoring.md**：心跳协议、停滞检测、自动恢复

## 与 Conjure 的集成

代理团队扩展了 Conjure 的委托模型：

| Conjure 模式 | Claude Code 代理团队的对应操作 |
|-----------------|----------------------|
| `delegation-core:task-assessed` | `agent-teams:team-created` |
| `delegation-core:handoff-planned` | `agent-teams:tasks-assigned` |
| `delegation-core:results-integrated` | `agent-teams:team-shutdown` |
| 外部 LLM 执行 | 由团队成员代理执行 |

首先使用 `Skill(conjure:delegation-core)` 来判断任务是否适合多代理协调，还是单服务委托。

## Worktree 隔离方案（Claude Code 2.1.49+）

对于修改文件的并发代理，`isolation: worktree` 提供了一种轻量级的替代方案，无需依赖基于文件系统的协调机制。每个代理都在自己的临时 Git worktree 中运行，从而避免了 `fcntl` 锁定或共享文件上的冲突。

- **何时优先使用 worktree**：代理处理重叠文件但不需要执行过程中的通信
- **何时优先使用代理团队通信**：代理需要根据彼此的进度协调发现或调整计划
- **结合使用**：使用代理团队进行协调，并为每个团队成员使用 `isolation: worktree` 以确保文件系统的安全性

## 故障排除

### 常见问题

**找不到 tmux**
通过包管理器安装：`brew install tmux` / `apt install tmux`

**锁文件失效**
如果代理在运行过程中崩溃，锁文件可能会持续存在。请手动删除 `~/.claude/teams/<team>/inboxes/` 或 `~/.claude/tasks/<team>/` 目录下的 `.lock` 文件。

**孤立任务**
被崩溃代理占用的任务会无限期地保持 `in_progress` 状态。请使用 `modules/health-monitoring.md` 中的心跳检测机制自动释放这些任务。健康监控协议会在 60 秒内检测到无响应的代理，并释放其任务以供重新分配。

**消息顺序问题**
文件系统的时间戳解析可能存在差异（HFS+ 的精度为 1 秒）。使用编号文件名或 UUID 排序的文件名以避免在快速发送消息时发生冲突。

**Bedrock/Vertex/Foundry（2.1.39 之前版本）的模型问题**
团队成员代理可能在企业级提供者上使用错误的模型标识符，导致 400 错误。请升级到 Claude Code 2.1.39+ 以确保所有提供者都能正确识别模型 ID。

**嵌套会话问题（2.1.39+）**
如果 `claude` 拒绝在现有会话中启动，请确保使用 tmux 窗口分割（而非子shell 调用）。这种设计是故意的——详情请参阅 `modules/spawning-patterns.md`。

## 退出标准

- 团队已创建并配置完成
- 团队成员已生成并注册到配置中
- 任务已创建并带有依赖关系图（无循环）
- 代理通过收件箱消息进行协调
- 优雅关闭过程已完成

---
> Source: [AgentWorkers/skills](https://github.com/AgentWorkers/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
