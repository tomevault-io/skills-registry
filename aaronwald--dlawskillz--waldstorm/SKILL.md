---
name: waldstorm
description: Multi-agent orchestration that analyzes tasks through specialized expert panels (security, performance, architecture, etc.), synthesizes prioritized actions, then plans and executes. Use when facing complex tasks requiring multiple perspectives, architectural decisions, new features needing security/performance/quality review, or user says "waldstorm", "expert review", "analyze this task". Use when this capability is needed.
metadata:
  author: aaronwald
---

# waldstorm

Multi-agent orchestration that brings together specialized experts to analyze, plan, and execute tasks with comprehensive coverage.

## Overview

1. Analyze the task and select 3-5 relevant experts
2. Create a team and dispatch expert teammates in parallel
3. Collect findings from teammate messages and synthesize into prioritized action items
4. **Write implementation plan to file** using `superpowers:writing-plans`
5. Execute with checkpoints and progress journaling

## Expert Panel

### General Agents (Built-in)

Each expert has a dedicated agent definition in `agents/` with frontmatter (name, description, tools, model, memory) and persona prompt:

| Agent | File | Focus Area |
|-------|------|------------|
| Senior Developer | `senior-developer.md` | Architecture, code quality, maintainability |
| DevOps Engineer | `devops-engineer.md` | CI/CD, deployment, observability |
| Code Reviewer | `code-reviewer.md` | Best practices, consistency, edge cases |
| Performance Expert | `performance-expert.md` | Bottlenecks, scalability, caching |
| Security Engineer | `security-engineer.md` | Vulnerabilities, auth, OWASP |
| QA/Testing Expert | `qa-testing-expert.md` | Test coverage, failure modes |
| Debugger/Troubleshooter | `debugger-troubleshooter.md` | Root cause analysis, logging |
| Database Expert | `database-expert.md` | Schema, queries, migrations |
| API Designer | `api-designer.md` | Interface contracts, versioning |
| Platform/Infra | `platform-infra.md` | Kubernetes, cloud architecture |
| Documentation Writer | `documentation-writer.md` | Clarity, examples, onboarding |
| Cost Analyst | `cost-analyst.md` | Resource efficiency, cloud spend |

### Domain-Specific Agents (Project-Local)

Projects can define domain-specific agents in their plugin `agents/` directories or in `.claude/agents/`.

**Discovery:** At task start, check for project-local agent definitions:
- Plugin agents: `plugins/*/agents/*.md`
- Project agents: `.claude/agents/*.md`

Each agent file uses frontmatter (name, description, tools, model, memory) followed by persona prompt and analysis instructions.

**Usage:** Domain agents are selected alongside general agents when their description triggers match the task. Spawn them as teammates like general agents.

## Instructions

### Step 1: Understand the Task

Ask the user to describe the task if not already provided. Gather:
- What needs to be accomplished
- Any constraints or requirements
- Relevant context (files, systems involved)

### Step 2: Discover Domain Agents

Check for project-local domain agents:
1. Look for plugin `agents/` directories and `.claude/agents/` in the project
2. If found, read agent descriptions to understand their specialties
3. Note available domain agents and their trigger keywords

Domain agents bring specialized knowledge that general agents lack (e.g., specific APIs, data models, deployment patterns).

### Step 3: Select Relevant Agents

**MANDATORY:** Always include the **QA/Testing Expert** (`dlaw:qa-testing-expert`) in every expert panel. Complete unit test coverage is required for all code changes.

Analyze the task domain and select 3-5 agents from both pools:
- **General agents** (built-in) for cross-cutting concerns
- **Domain agents** (project-local) for specialized knowledge

Use this guide for general agents:

| Task Domain | Recommended Agents |
|-------------|---------------------|
| New feature | Senior Dev, Code Reviewer, QA, Security |
| Database work | Database Expert, Performance, Senior Dev |
| API changes | API Designer, Security, Code Reviewer |
| Infrastructure | DevOps, Platform/Infra, Cost Analyst, Security |
| Bug fix | Debugger, Senior Dev, QA |
| Performance issue | Performance Expert, Database, Debugger |
| Security audit | Security Engineer, Code Reviewer |
| Documentation | Documentation Writer, Senior Dev |
| Cost optimization | Cost Analyst, Platform/Infra, DevOps |

Announce: "Selected agents for this task: [list agents and why]"

If domain agents are selected, note their source.

### Step 4: Create Team and Dispatch Agent Teammates

Create a team and spawn agent teammates for parallel analysis:

1. **Create team:** Use `TeamCreate` with `team_name: "waldstorm-{task-slug}"` (e.g., `waldstorm-add-auth`)
2. **Create tasks:** Use `TaskCreate` for each agent's analysis task, including:
   - Subject: `"{Agent Name} analysis"`
   - Description: the task description + what the agent should analyze
3. **Spawn teammates:** Use the `Task` tool with `team_name` and `name` params to launch one teammate per agent. Each teammate's prompt should include the agent's persona and instructions from their agent definition file in `agents/`.
4. **Assign tasks:** Use `TaskUpdate` with `owner` to assign each task to its teammate

Each agent returns via message:
- **Concerns** (prioritized HIGH/MEDIUM/LOW)
- **Recommendations** (specific actions)
- **Questions** (clarifications needed)

### Step 5: Collect and Synthesize

Wait for all teammate messages. Use `TaskList` to verify all expert tasks are completed.

Gather all expert outputs from messages and synthesize into prioritized action items:

```markdown
## Prioritized Action Items

### Critical
1. [Expert tags] Description of critical action

### Important
2. [Expert tags] Description of important action

### Nice to Have
3. [Expert tags] Description of optional improvement

### Conflicts to Resolve
- [Expert A] recommends X; [Expert B] flags concern Y
  - Options presented for user decision
```

Present synthesis to user. Ask if they want to:
- Proceed with all recommendations
- Modify priorities
- Exclude certain items
- Add additional concerns

### Step 6: ECC Code Review of Expert Findings

**OPTIONAL but recommended:** After synthesis and before planning, invoke `everything-claude-code:code-review` to get an independent review of the proposed changes. This catches issues the expert panel may have missed by applying ECC's structured review checklist (security, patterns, error handling, type safety, test coverage).

Pass the synthesized action items as context. If the ECC reviewer raises Critical issues, fold them into the action items before planning.

### Step 7: Generate Implementation Plan

**REQUIRED:** Invoke `superpowers:writing-plans` to write the implementation plan to file.

The plan MUST be saved to: `docs/plans/YYYY-MM-DD-<feature-name>.md`

The plan should:
- Incorporate expert recommendations
- Order tasks by dependency and priority
- Include checkpoints for review
- Note which expert's recommendation each task addresses
- Follow the bite-sized task granularity from writing-plans (each step is one action)

### Step 8: Execute with Checkpoints

Invoke `superpowers:executing-plans` to begin implementation.

During execution:
- Journal all TODOs to a todo file for the plan
- Track completed items as you go
- Pause at checkpoints for review
- Flag if implementation reveals new concerns

### Step 9: Cleanup

After execution is complete:
1. Use `SendMessage` with `type: "shutdown_request"` to gracefully shut down all teammates
2. Use `TeamDelete` to clean up the team and task list

## Example Flow

```
User: "Add user authentication to the API"

waldstorm:
1. Selected experts: Security Engineer, API Designer, Senior Dev, QA
2. TeamCreate: "waldstorm-add-auth"
3. Spawning 4 expert teammates in parallel...
   - Each reads their persona from experts/*.md
   - Each analyzes and sends findings via SendMessage
4. Synthesis (from teammate messages):
   - [CRITICAL] [Security] Use bcrypt for password hashing, not MD5
   - [CRITICAL] [Security, API] Implement rate limiting on auth endpoints
   - [HIGH] [API] Use JWT with short expiry + refresh tokens
   - [HIGH] [QA] Add integration tests for auth flows
   - [MEDIUM] [Senior Dev] Extract auth logic into dedicated service

5. User approves, writing plan...
6. Executing plan with checkpoints...
7. Shutdown teammates, TeamDelete
```

## Model Routing

Use ECC's `agentic-engineering` model routing pattern to optimize cost and quality:

| Context | Model | Rationale |
|---------|-------|-----------|
| Expert analysis (teammates) | sonnet | Balanced speed/quality for focused analysis tasks |
| Synthesis & conflict resolution | opus | Deep reasoning needed for cross-expert synthesis |
| Implementation plan writing | opus | Architecture decisions require careful reasoning |
| Boilerplate/scaffolding execution | haiku | Fast, cheap for mechanical code generation |
| Complex implementation tasks | sonnet | Good balance for implementation work |
| Final review & verification | opus | High-stakes quality gate |

Pass the `model` parameter when spawning teammates via the `Task` tool to apply routing.

## Skills & Plugins Used

- `superpowers:writing-plans` - Convert recommendations to implementation plan
- `superpowers:executing-plans` - Execute with review checkpoints
- `everything-claude-code:code-review` - Independent structured review of proposed changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aaronwald) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
