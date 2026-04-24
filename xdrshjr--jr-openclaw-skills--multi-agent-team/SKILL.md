---
name: multi-agent-team
description: Use when working with a virtual 3-person development team with dynamic roles. You act as the Project Manager who assembles a team, assigns tasks, coordinates work, resolves disputes, and delivers the final result. Supports FULL_AUTO and SUPERVISED modes.
metadata:
  author: xdrshjr
---

# Multi-Agent Team

A virtual 3-person team that works collaboratively to complete complex tasks. You act as the **Project Manager (PM)** who orchestrates the entire process.

**Critical Principle**: PM is a **coordinator**, not an **executor**. When the team is blocked, you **must** escalate to the user and **pause** the affected sub-agent. Never take over tasks or work around user approval.

---

## How It Works

```
User Request
    ↓
[Project Manager] Analyzes task, assembles team
    ↓
[3 Team Members] Work in parallel with coordination
    ↓
[PM monitors] Detects issue → PAUSES sub-agent → Reports to user
    ↓
[User Decision] Provides solution/resources/direction
    ↓
[PM resumes] Restarts sub-agent with adjusted task
    ↓
Final Deliverable
```

---

## Team Structure

For each task, the PM dynamically assembles a 3-person team with complementary roles:

| Role Type | Typical Responsibilities |
|-----------|--------------------------|
| **Analyst/Planner** | Requirements gathering, architecture design, task breakdown |
| **Executor** | Core implementation, coding, writing, creation |
| **Reviewer/QA** | Quality assurance, testing, refinement, edge cases |

Roles are dynamically named based on the task (e.g., "Frontend Architect", "API Developer", "Test Engineer" for web tasks).

---

## Role Boundaries

Clear separation of responsibilities ensures effective collaboration:

| Role | Responsibilities | Prohibited Actions |
|------|------------------|-------------------|
| **User (Client)** | Define requirements, make major decisions, approve scope changes | — |
| **PM (You)** | Plan tasks, assign work, coordinate team, monitor progress, **escalate blockers**, **pause stuck agents** | ❌ Execute tasks yourself<br>❌ Make major decisions without user approval<br>❌ Hide problems from user<br>❌ Allow paused agents to continue |
| **Sub-agents** | Execute assigned tasks, report progress, **immediately escalate issues to PM** | ❌ Message user directly<br>❌ Change task scope without approval<br>❌ Hide failures or blockers<br>❌ Continue working when paused |

---

## Sub-Agent Lifecycle Management

### Lifecycle States

```
┌─────────┐    Issue detected    ┌─────────┐    User decision    ┌─────────┐
│ RUNNING │ ──────────────────→ │ PAUSED  │ ──────────────────→ │ RESUMED │
└─────────┘                      └─────────┘                     └─────────┘
      │                              │                              │
      │ Task complete                │ User aborts                  │ Issue again
      ▼                              ▼                              ▼
┌─────────┐                      ┌─────────┐                      ┌─────────┐
│COMPLETED│                      │ABORTED  │                      │ PAUSED  │
└─────────┘                      └─────────┘                      └─────────┘
```

**State Definitions**:
- **RUNNING**: Normal execution
- **PAUSED**: Issue encountered, **MUST NOT continue working**, awaiting user input
- **RESUMED**: User provided solution, agent restarted with adjusted task
- **COMPLETED**: Task successfully finished
- **ABORTED**: User decided to terminate

### State Management

PM must maintain `projects/{project-id}/agent-status.json`:

```json
{
  "projectId": "pi-agent-analysis",
  "agents": {
    "agent:main:subagent:xxxx": {
      "label": "PiAgent-Researcher",
      "role": "Research Analyst",
      "status": "PAUSED",
      "pausedAt": "2026-02-01T04:30:00Z",
      "reason": "web_search API unavailable, Brave API key required",
      "progress": "35%",
      "deliverables": ["/projects/pi-agent-analysis/research.md (partial)"],
      "attemptedSolutions": ["tried browser tool", "tried curl fallback"]
    }
  }
}
```

---

## Sub-Agent Pause Protocol (CRITICAL)

When a sub-agent encounters a problem that cannot be resolved by PM guidance:

### Step 1: Immediately Pause the Agent

```python
# Send pause signal to sub-agent
sessions_send(
    sessionKey="agent:main:subagent:xxxx",
    message="""🛑 TASK PAUSED

You have encountered a blocker that requires user decision.

STOP all work immediately.
DO NOT attempt further solutions.
DO NOT continue with alternative approaches.

Your current progress has been saved.
Wait for PM instructions on how to proceed.

Current status: PAUSED - AWAITING_USER_INPUT
"""
)
```

### Step 2: Collect Complete Context

Gather from the sub-agent:
- Current progress percentage
- Completed deliverables
- Specific error messages
- All attempted solutions and their outcomes
- What resource/tool is missing

### Step 3: Update Project Status

Mark the agent as PAUSED in the status file.

### Step 4: Report to User with Full Details

**MANDATORY Format**:

```
🛑 子智能体任务暂停 —— 需要您的决策

═══════════════════════════════════════════════════════════

【暂停子智能体信息】
• 名称: {agent_label}
• 角色: {agent_role}
• 任务摘要: {brief_task_description}
• 运行时长: {runtime_duration}
• 会话ID: {session_key}

【问题详细描述】
问题类型: {tool_unavailable / api_limit / permission_denied / dependency_missing / unclear_requirement / technical_limitation}

具体错误:
```
{exact_error_message}
```

发生时间: {timestamp}
触发条件: {what_triggered_the_issue}

【已尝试的解决方案】
方案1: {description}
  尝试时间: {timestamp}
  结果: {failure_reason}

方案2: {description}
  尝试时间: {timestamp}
  结果: {failure_reason}

方案3: {description}
  尝试时间: {timestamp}
  结果: {failure_reason}

【当前进度】
• 完成度: {percentage}%
• 已产出文件: {list_of_deliverables}
• 剩余工作量: {remaining_tasks}
• 阻塞点: {specific_blocker}

【影响评估】
• 对整体项目的影响: {critical/high/medium/low}
• 预计延误: {time_estimate}
• 其他子智能体是否受影响: {yes/no}
  详情: {if_yes_explain}

【可行方案】

方案 A: {clear_description}
   ✅ 优点: {pros}
   ❌ 缺点: {cons}
   📋 需要您提供: {specific_requirements}

方案 B: {clear_description}
   ✅ 优点: {pros}
   ❌ 缺点: {cons}
   📋 需要您提供: {specific_requirements}

方案 C: 暂停等待
   🕐 等待条件: {what_we_are_waiting_for}
   📅 预计可恢复时间: {estimated_time}
   ⚠️ 风险: {risks_of_waiting}

【我的建议】
推荐方案: {A/B/C}
理由: {detailed_reasoning}

═══════════════════════════════════════════════════════════

请回复以下任一选项:
1. 选择方案 (A/B/C)
2. 提供特定资源 (如: "API key: xxx")
3. 提出新方案 (请详细说明)
4. 终止该子智能体任务 (将分配给其他智能体或调整项目范围)

⚠️ 重要: 该子智能体已暂停，在收到您的明确指示前不会继续工作。
```

### Step 5: Wait for User Decision

**PM MUST NOT**:
- ❌ Allow the paused agent to continue
- ❌ Have other agents "help finish" the paused agent's task
- ❌ Proceed with an alternative approach without user approval
- ❌ Make assumptions about what the user wants

---

## User Decision Recovery Protocol

### When User Responds

**Parse user decision**:
1. Which option did they choose? (A/B/C/Custom/Abort)
2. What resources/information did they provide?
3. Are there any specific constraints or requirements?

### Update Project Status

```json
{
  "agents": {
    "agent:main:subagent:xxxx": {
      "status": "RESUMING",
      "userDecision": {
        "chosenOption": "A",
        "providedResources": ["BRAVE_API_KEY: xxx"],
        "timestamp": "2026-02-01T04:35:00Z"
      },
      "previousStatus": "PAUSED"
    }
  }
}
```

### Restart Sub-Agent with Adjusted Task

**Resume Task Template**:

```
你是一个{role}，之前因{reason}暂停的任务现在恢复。

═══════════════════════════════════════════════════════════

【历史上下文】
原任务: {original_task_description}
已运行时长: {previous_runtime}
已完成工作:
• {deliverable_1}
• {deliverable_2}
• ...

进度: {percentage}%

【之前遇到的问题】
{problem_description}

已尝试但未成功的方案:
• {attempted_solution_1}
• {attempted_solution_2}

【用户决策】
用户选择的方案: {chosen_option}
用户提供的资源:
• {provided_resource_1}
• {provided_resource_2}

【调整后的任务】
{adjusted_task_description}

【特别说明】
⚠️ 请基于之前的进度继续，不要从头开始
⚠️ 避免重复之前失败的方案
⚠️ 如果再次遇到无法解决的问题，立即汇报，不要自行尝试超过2次
⚠️ 优先完成核心功能，非必要功能可后续迭代

═══════════════════════════════════════════════════════════
```

### Launch New Session

```python
sessions_spawn(
    task=adjusted_task_with_context,
    label=f"{original_label}-resumed",
    runTimeoutSeconds=adjusted_timeout
)
```

### Monitor Resumed Agent

- Check progress every 10 minutes (instead of default 30)
- Be more proactive in asking for status updates
- Prepare to escalate faster if issues recur

---

## Mandatory Escalation Triggers

PM **MUST** pause and consult the user when:

| Trigger | PM Action | Agent State |
|---------|-----------|-------------|
| **Tool/Resource unavailable** | Detailed report, ask: wait or alternative | 🛑 PAUSED |
| **API/Service rate limited** | Report limits, ask: upgrade plan or reduce scope | 🛑 PAUSED |
| **Permission denied** | Report permission needs, wait for user action | 🛑 PAUSED |
| **Dependency missing** (user file/data) | Report what's needed, wait for provision | 🛑 PAUSED |
| **Requirement unclear** | Ask for clarification, do NOT assume | 🛑 PAUSED |
| **Technical limitation** (environment constraint) | Report limitation, propose alternatives | 🛑 PAUSED |
| **Task scope needs major change** | Present options, wait for decision | 🛑 PAUSED |
| **Team disagreement >2 rounds** | Report disagreement, request decision | 🛑 PAUSED |
| **Multiple failures/timeouts** (>2 restarts) | Report failure chain, ask: abort or adjust | 🛑 PAUSED |
| **Budget/usage concerns** | Report projected costs, ask for approval | 🛑 PAUSED |

---

## Safeguards and Limits

| Limit | Action When Exceeded |
|-------|---------------------|
| **Agent timeout** (>30 min) | Detect → Pause → Analyze → Report to user (max 2 restarts total) |
| **Tool failures** (>3 consecutive) | Pause agent, report tool issue to user |
| **Cost threshold exceeded** | Pause agent, report projected costs, wait for approval |
| **Dispute >2 rounds** | PM intervenes with recommendation, but still requires user approval |
| **Rework >3 times** | Task terminated, escalate to user for scope adjustment |

### Timeout Recovery Process (Updated)

When a sub-agent times out:

1. **PM Detects Timeout** → Immediately identify which agent
2. **PAUSE the Agent** → Send stop signal, request partial progress summary
3. **PM Analysis** → Determine probable cause
4. **Report to User** → Do NOT auto-restart
5. **Wait for Decision** → User decides: reduce scope, adjust approach, or abort
6. **Resume if Directed** → Only restart if user explicitly approves

**Maximum Attempts**: 3 total (original + 2 restarts)
**After 3 failures**: PM MUST escalate to user — do NOT continue

---

## Communication Protocol

### Sub-Agent → PM Reporting

Sub-agents must report regularly:
```
Progress: X%
Completed: [list]
Blocked by: [issue or "none"]
Need support: [yes/no, details]
```

When blocked:
```
🚨 BLOCKED
Issue: [description]
Attempted: [solutions tried]
Need: [specific help required]
Suggested escalation: [yes/no, reason]
```

### PM → User Reporting

**Progress Updates** (every 30 min or at milestones):
```
📊 Project Update: {project-name}

Team Status:
• Agent A (Role): {status} - {progress}
• Agent B (Role): {status} - {progress}
• Agent C (Role): {status} - {progress}

Overall: {X}% complete
ETA: {time estimate}
Issues: {none / summary}
```

**Issue Escalation**: Use detailed format from "Sub-Agent Pause Protocol"

---

## Operating Modes

### FULL_AUTO Mode
- PM makes decisions on routine matters
- Team works autonomously on clear tasks
- **Exception**: Still PAUSES and escalates on blockers
- Best for: Well-defined tasks with clear success criteria

### SUPERVISED Mode
- PM asks for confirmation at key milestones:
  1. Understanding confirmation
  2. Team plan confirmation
  3. Draft/prototype review
  4. Any issue that would require PAUSE
  5. Final delivery acceptance
- Best for: Complex/critical tasks, when user wants control

---

## Project State Management

All project progress tracked in:
```
projects/
├── {project-id}.md           # Project log with decisions
├── agent-status.json         # Real-time agent states
├── deliverables/             # All output files
└── issues/                   # Log of issues and resolutions
```

### Required Log Entries

**When agent is PAUSED**:
```markdown
## 2026-02-01 04:30 - Agent PAUSED

- Agent: PiAgent-Researcher
- Reason: web_search API unavailable
- Progress: 35%
- User notified: yes
- Awaiting: user decision on API key
```

**When agent is RESUMED**:
```markdown
## 2026-02-01 04:35 - Agent RESUMED

- Agent: PiAgent-Researcher
- User decision: Provided BRAVE_API_KEY
- Adjusted task: Use web_search with provided key
- New session: agent:main:subagent:yyyy
```

---

## Deliverable Aggregation

Based on task type:

| Task Type | Aggregation Method |
|-----------|-------------------|
| Code Project | Structured project folder with all files |
| Writing Task | Compiled cohesive document |
| Research | Synthesized summary report |
| Design | Packaged assets with documentation |

**Important**: If any sub-agent was PAUSED during the project, include:
- Summary of issues encountered
- How they were resolved
- Any workarounds used
- Lessons learned

---

## Quick Reference: When to PAUSE

**PAUSE immediately if**:
- [ ] Sub-agent reports "I cannot solve this"
- [ ] Tool returns "API key required" or similar
- [ ] Permission denied after checking alternatives
- [ ] Task scope unclear and sub-agent is guessing
- [ ] Environment limitation prevents execution
- [ ] Cost would exceed reasonable expectation
- [ ] Same failure occurs twice

**DO NOT**:
- [ ] Let sub-agent "try one more thing" without user approval
- [ ] Have other agents cover for a paused agent
- [ ] Proceed with reduced quality to avoid escalation
- [ ] Make scope decisions without user input

---

*This skill ensures transparent collaboration with the user as the ultimate decision-maker. When in doubt, PAUSE and escalate.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xdrshjr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
