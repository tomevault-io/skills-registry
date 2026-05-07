---
name: tool-advisor
description: Analyzes prompts and recommends optimal Claude Code tools, skills, agents, and orchestration patterns. Suggests harness patterns for complex tasks and offers installation when tools are missing.
argument-hint: <prompt or task description>
metadata:
  author: aerok
  version: "2.1.0"
aliases:
  - ta
---

# Tool Advisor v2.1 - Optimal Tool Recommendation Skill

Analyzes user's natural language prompts to provide:
1. **Optimal Tool Recommendation** - Select the best tool from locally installed options
2. **Harness Pattern Recommendation** - Suggest auto-loop structures for complex long-running tasks
3. **Tool Installation Suggestions** - Search web and suggest installation when needed tools are missing

## Analysis Process

### Phase 1: Check Local Tool Inventory

**Always run first:**

```bash
# 1. Check installed plugins
cat ~/.claude/plugins/installed_plugins.json | jq 'keys'

# 2. Check installed skills
ls ~/.claude/skills/

# 3. Check installed agents
ls ~/.claude/agents/
```

Use these commands to identify currently installed tools.

### Phase 2: Assess Task Complexity and Harness Needs

| Complexity | Characteristics | Recommended Approach | Harness Needed |
|------------|-----------------|---------------------|----------------|
| **Simple** | 1-2 files, clear task | Direct tools (Read/Edit) | No |
| **Medium** | 3-5 files, multiple steps | Task agent or skill | No |
| **Complex** | 5+ files, design+implement+test | `/feature-dev` + workflow | Optional |
| **Long-running** | Loop until goal, multi-stage validation | **Harness pattern required** | **Yes** |

### Phase 3: Determine Harness Requirement

**Signals that indicate harness is needed:**
- "until complete", "keep trying", "repeatedly"
- "review → design → develop → QA → test" full cycle
- "automatically", "on its own", "long-term"
- Multiple agent coordination required
- Auto-retry/fix on failure needed

**Harness Pattern Types:**

| Pattern | Description | Tool |
|---------|-------------|------|
| **Ralph Pattern** | Autonomous loop until goal completion | `ralph-orchestrator` |
| **RIPER Workflow** | Research→Innovate→Plan→Execute→Review cycle | `riper-workflow` |
| **Spec Workflow** | Requirements→Design→Implement→Verify pipeline | `spec-workflow` |
| **Full-stack Orchestration** | Backend→Frontend→Test→Deploy coordination | `full-stack-orchestration` |
| **Claude Flow** | Distributed agent swarm | `claude-flow` |

### Phase 4: Suggest Installation When Tools Are Missing

When appropriate local tools are not available:

1. **Search for latest tools via WebSearch**
```
WebSearch: "Claude Code [task-type] plugin workflow 2026"
```

2. **Suggest installation to user (Human-in-the-loop)**
```markdown
## Recommended Tool Installation

No suitable tools found locally for [task].

### Recommended Installation List

| Tool | Purpose | Install Command |
|------|---------|-----------------|
| [tool1] | [description] | `/plugin install [tool1]` |
| [tool2] | [description] | `/plugin install [tool2]` |

**Would you like to install?** (yes/no/some)
```

3. **Proceed with installation after user approval**

---

## Output Format

Prompt: `$ARGUMENTS`

### Analysis Result Template

```markdown
## Prompt Analysis Result

### 1. Task Classification
- **Primary Type**: [Development/Review/Exploration/Business/...]
- **Secondary Type**: [...]
- **Complexity**: [Simple/Medium/Complex/Long-running]

### 2. Harness Requirement
- **Required**: [Yes/No]
- **Reason**: [Why needed or not needed]
- **Recommended Pattern**: [Ralph/RIPER/Spec/Full-stack/None]

### 3. Local Tool Status
- **Installed**: [List of available tools]
- **Missing**: [Tools needed but not installed]

### 4. Recommendation

#### A. When local tools are sufficient

**Optimal Recommendation**: [tool name]
**How to use**:
```
[command or prompt]
```

#### B. When additional tool installation is needed

**Recommended Tools to Install**:

| Tool | Purpose | Installation |
|------|---------|--------------|
| [tool] | [description] | `/plugin marketplace add [source]` then `/plugin install [tool]` |

**Usage after installation**:
```
[command]
```

**Would you like to install now?**

### 5. Workflow Suggestion (if applicable)

```
[Step-by-step execution order]
```

---

## 🎯 Quick Action

| Your Situation | Copy & Paste |
|----------------|--------------|
| [situation1] | `[command1]` |
| [situation2] | `[command2]` |
| [situation3] | `[command3]` |

**→ Recommended: "[preferred option]"** ([reason])
```

---

## Harness Pattern Details

### Ralph Pattern (Autonomous Loop)

**When to use**: When automatic repetition is needed until goal completion

**Structure**:
```
┌─────────────────────────────────────────┐
│              Ralph Harness               │
├─────────────────────────────────────────┤
│                                          │
│  ┌────────┐  ┌─────────┐  ┌────────┐    │
│  │ Analyze │─▶│ Execute │─▶│ Verify │    │
│  └────────┘  └─────────┘  └───┬────┘    │
│       ▲                       │          │
│       │     Retry on failure  │          │
│       └───────────────────────┘          │
│                                          │
│  Exit: Goal achieved OR max iterations   │
│  Safety: circuit breaker, rate limit     │
│                                          │
└─────────────────────────────────────────┘
```

**Check installation**:
```bash
# Check if ralph-orchestrator is installed
cat ~/.claude/plugins/installed_plugins.json | grep -i ralph
```

**If not installed**:
```bash
/plugin marketplace add wshobson/agents
/plugin install ralph-orchestrator
```

### RIPER Workflow

**When to use**: When systematic step-by-step progression is needed

**Structure**:
```
Research → Innovate → Plan → Execute → Review
    │                                    │
    └────────── Feedback Loop ───────────┘
```

### Full-stack Orchestration

**When to use**: When multiple specialized agents need coordination

**Structure**:
```
Phase 1: Design (architect, database-designer)
    ↓
Phase 2: Implement (backend, frontend, database) [parallel]
    ↓
Phase 3: Test (unit-tester, e2e-tester, security-auditor) [parallel]
    ↓
Phase 4: Deploy (deployment-engineer, performance-engineer)
```

---

## Core Decision Tree

```
User Prompt
      │
      ▼
┌─────────────────┐
│ Harness needed? │
└────────┬────────┘
         │
    ┌────┴────┐
    ▼         ▼
  [Yes]      [No]
    │          │
    ▼          ▼
Is harness  Check complexity
installed       │
locally?   ┌────┴────┐
    │      ▼         ▼
┌───┴───┐ [Complex] [Simple/Medium]
▼       ▼    │           │
[Yes]  [No]  │           ▼
  │      │   │      Local tools
  │      ▼   │      sufficient?
  │  WebSearch│           │
  │  + suggest│      ┌────┴────┐
  │  install  │      ▼         ▼
  │      │    │    [Yes]      [No]
  ▼      ▼    │      │          │
Guide  Install│      │      WebSearch
usage  then   │      │      + suggest
       use    │      ▼      install
              │  Direct tool    │
              │  or skill       ▼
              │    use      Install
              │             then use
              ▼
         /feature-dev
         or harness
```

---

## Execution Guidelines

1. **Phase 1**: Check local tool inventory via Bash
2. **Phase 2**: Analyze prompt (complexity, harness needs)
3. **Phase 3**: Determine if local tools are sufficient
4. **Phase 4**: If insufficient, search tools via WebSearch → suggest installation
5. **Phase 5**: Provide final recommendation and usage guide
6. **Phase 6**: Present Quick Action (copyable command table)

**Important**:
- Tool installation only proceeds after user approval (Human-in-the-loop)
- **Always include "🎯 Quick Action" section at the end** (so user can take immediate action)

---

## Example: Complex Long-running Task

**Input**:
```
Review entrypoint.sh and refactor to daily refresh structure if needed.
Check if DeepSearch API can replace current approach and implement using existing patterns.
Think through this in plan mode.
```

**Output**:
```markdown
## Prompt Analysis Result

### 1. Task Classification
- **Primary Type**: Feature Development/Refactoring
- **Secondary Type**: Code exploration, external doc reference, pattern analysis
- **Complexity**: Complex (5+ files, design needed)

### 2. Harness Requirement
- **Required**: Optional (single feature but multi-stage)
- **Reason**: Design→Implement→Test cycle but one-time task
- **Recommended Pattern**: None (but consider Ralph if failures occur)

### 3. Local Tool Status
- **Installed**: feature-dev, full-stack-orchestration, comprehensive-review
- **Missing**: None (sufficient)

### 4. Recommendation

**Optimal Recommendation**: `/feature-dev`

**Reason**:
- "plan mode" request → design-first approach
- Existing pattern analysis → need to implement same pattern
- Guided flow allows mid-process verification

**How to use**:
```
/feature-dev

Prompt to enter:
Review the stock data update logic in entrypoint.sh
and refactor to DeepSearch API-based daily refresh structure.
Reference doc: /Users/aerok/.../02-company.md
Implement using same pattern as existing deepsearch calls.
```

### 5. Alternatives
1. **EnterPlanMode** - When you want more flexible planning
2. **Ralph + feature-dev** - When you want auto-verification loop after implementation

---

## 🎯 Quick Action

| Your Situation | Copy & Paste |
|----------------|--------------|
| Want to see plan first | `Plan the entrypoint.sh refactoring in Plan Mode` |
| Want guided development | Use `/feature-dev` then enter the prompt above |
| Just implement it | `Refactor entrypoint.sh to use DeepSearch API` |

**→ Recommended: "Plan first"** (5+ files, design needed)
```

Prompt to analyze: $ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
