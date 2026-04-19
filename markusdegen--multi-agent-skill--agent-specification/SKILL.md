---
name: agent-specification
description: This skill should be used when the user asks to "write agent spec", "define agent role", "specify an agent", "agent specification", "what should my agent do", "agent inputs outputs", "agent design discovery", "design my agent", or needs to create a proper agent definition. Provides discovery questions and specification format that prevents 42% of MAS failures. Use when this capability is needed.
metadata:
  author: markusdegen
---

# Agent Specification

## Before You Specify: Discovery

**Before writing a formal specification**, work through the discovery questions to understand:
- What the agent needs to achieve and who uses it
- Memory requirements (what to remember between steps/sessions)
- Reliability boundaries (what cannot fail, who approves actions)
- Economics (task volume, cost tolerance, model routing)
- Tool requirements (actions, audit trail)
- Testing strategy (success metrics, observability)

See **`references/discovery-questions.md`** for the complete discovery framework.

## Why Specification Matters

Specification issues cause **41.77% of MAS failures**. Most failures stem from:
- Ambiguous agent roles
- Unclear input/output contracts
- Missing forbidden actions
- Vague success criteria

## 12 Factor Agents Principles

The 12 Factor Agents framework provides engineering principles that complement specification best practices.

### Factor 1: Natural Language to Tool Calls

LLMs transform natural language into structured tool calls (JSON). When specifying agents, define:

1. **Input format**: Natural language or structured input?
2. **Output format**: Always structured (JSON schema)
3. **Tool call schema**: Exact format the agent will produce

**Tool Call Output Specification:**
```json
{
  "tool_name": "create_task",
  "parameters": {
    "title": "string (required)",
    "priority": "high|medium|low",
    "assignee": "string (optional)"
  }
}
```

**Key insight**: The "magic" is in reliable structured output generation. Specify schemas explicitly.

### Factor 2: Own Your Prompts

**Principle**: LLMs are pure functions (tokens in → tokens out). Don't let frameworks abstract away prompts.

When writing agent specifications:
1. **Include the system prompt** in the spec document
2. **Version control prompts** alongside agent code
3. **Document prompt engineering decisions** and experiments

**Prompt Ownership Checklist:**
- [ ] System prompt is visible, not hidden in framework
- [ ] Prompt changes require code review
- [ ] A/B testing capability for prompt variants
- [ ] Prompt is part of agent version (spec v2.1 = prompt v2.1)

**Anti-pattern**: "Let the framework handle prompts"
**Pattern**: "The prompt IS the agent specification"

### Factor 4: Tools Are Structured Outputs

**Principle**: "Tool-Use" is just JSON output + deterministic code execution. Demystify it.

A tool call is:
1. Agent outputs structured JSON (the "call")
2. Deterministic code executes based on JSON (the "use")
3. Result feeds back as context

**Tool Specification Template:**
```markdown
## Tool: [ToolName]

### Call Schema (Agent Output)
{
  "action": "tool_name",
  "params": { ... }
}

### Execution (Deterministic Code)
[What happens when this JSON is received]

### Result Schema (Context Addition)
{
  "success": boolean,
  "result": { ... },
  "error": { ... } | null
}
```

### Factor 7: Contact Humans with Tools

**Principle**: Human-in-the-loop is a first-class pattern. Agents should request human input as a tool call.

**Human Contact Tool Specification:**
```json
{
  "action": "request_human_input",
  "params": {
    "question": "What should I do about X?",
    "context": "Relevant context for decision",
    "options": ["Option A", "Option B", "Escalate"],
    "urgency": "blocking|async",
    "timeout_action": "wait|default|escalate"
  }
}
```

**Execution behavior:**
- **Blocking**: Pause agent, notify human, wait for response
- **Async**: Continue with other tasks, process response when received

**When to include Human Contact Tool:**
- High-stakes decisions requiring judgment
- Ambiguous requirements needing clarification
- Compliance/approval workflows
- Error recovery beyond agent capability

See **`references/spec-templates.md`** for the complete Human-in-the-Loop Agent Template.

## The Agent Specification Template

Every agent must have these five elements:

### 1. Explicit Role (What It IS)

Define the agent's identity, not its tasks:

**Bad:**
> "You are a planner helping the system."

**Good:**
> "You are a Planner. You produce task graphs with explicit dependencies. You do not execute tasks. Output only JSON."

### 2. Inputs It May Read

Enumerate all allowed information sources:

```markdown
## Inputs
- User requirements (natural language)
- System state from blackboard (read-only)
- Configuration parameters
- Previous agent outputs (specifically: Validator output)
```

### 3. Outputs It Must Produce

Define the exact output format:

```markdown
## Outputs
- Task graph in JSON format
- Each task has: id, description, dependencies[], status
- No prose explanations in output
- Must include confidence score (0-1)
```

### 4. Forbidden Actions

Explicitly state what the agent cannot do:

```markdown
## Forbidden Actions
- DO NOT execute tasks (only plan)
- DO NOT modify system state directly
- DO NOT communicate with external services
- DO NOT make assumptions about missing inputs
```

### 5. Success Criteria

Define measurable completion conditions:

```markdown
## Success Criteria
- All user requirements mapped to tasks
- No circular dependencies in task graph
- Each task is atomic (single responsibility)
- Confidence score ≥ 0.8 for all tasks
```

## Complete Specification Example

```markdown
# Agent: TaskPlanner

## Role
TaskPlanner decomposes user requirements into executable task graphs.
It analyzes requirements, identifies dependencies, and outputs structured plans.
It does not execute tasks or modify system state.

## Inputs
- User requirements (natural language, from orchestrator)
- Domain constraints (from configuration)
- Previous execution feedback (optional, from Verifier)

## Outputs
- JSON task graph with schema:
  {
    "tasks": [{
      "id": "string",
      "description": "string",
      "dependencies": ["task_id"],
      "estimated_complexity": "low|medium|high",
      "assigned_executor": "string|null"
    }],
    "metadata": {
      "total_tasks": number,
      "parallel_groups": number,
      "confidence": number
    }
  }

## Forbidden Actions
- Executing any task
- Modifying external state
- Making API calls
- Assuming missing requirements
- Outputting non-JSON content

## Success Criteria
- All requirements have corresponding tasks
- No orphan tasks (all connected to graph)
- No circular dependencies
- Confidence ≥ 0.8
- Valid JSON output
```

## Separating Task Spec from Coordination Spec

A common failure is mixing WHAT agents do with HOW they interact.

### Task Specification (per agent)

Defines individual agent behavior:
- Role and responsibilities
- Input/output contracts
- Constraints and boundaries

### Coordination Specification (system-level)

Defines agent interactions:
- Who speaks first
- Who can overwrite state
- Who resolves conflicts
- When communication stops

Example coordination spec:

```markdown
# Coordination Protocol

## Sequence
1. Orchestrator receives user request
2. Orchestrator dispatches to Planner
3. Planner outputs task graph to blackboard
4. Executors claim tasks (no conflicts - first-come)
5. Executors write results to blackboard
6. Verifier reads all results, produces verdict
7. If verdict = FAIL, Planner re-plans with feedback
8. If verdict = PASS, Orchestrator returns result

## State Ownership
- Blackboard: Orchestrator (read/write)
- Task claims: Executors (write own claims only)
- Results: Executors (write own results only)
- Verdicts: Verifier (write only)

## Conflict Resolution
- Task claim conflicts: First timestamp wins
- Result conflicts: Verifier decides
- Deadlock: Orchestrator timeout (30s) triggers re-plan
```

## Role Design Principles

### Design Roles, Not Personalities

**Bad (personality-based):**
- Creative agent
- Smart agent
- Careful agent

**Good (function-based):**
- Planner (decomposes)
- Executor (acts)
- Critic (finds flaws)
- Verifier (checks evidence)

### Functional Orthogonality

Each agent should have a distinct, non-overlapping function:

| Agent | Function | Cannot Do |
|-------|----------|-----------|
| Planner | Decompose tasks | Execute tasks |
| Executor | Execute tasks | Plan or verify |
| Verifier | Check results | Execute or plan |
| Critic | Find problems | Fix problems |

### Prevent Silent Ignoring

Require explicit acknowledgment of inputs:

```markdown
## Input Acknowledgment (Required)
Before processing, explicitly state:
"Received: [input type] from [source]"
"Using: [specific artifact] version [X]"

Example:
"Received: Task graph from Planner"
"Using: Plan v3, 12 tasks, confidence 0.92"
```

## Making State Explicit

Never rely on implicit shared context.

### State Requirements

1. **Shared blackboard/memory store**
2. **Versioned state updates**
3. **Read/write permissions per agent**

### State Schema Example

```json
{
  "blackboard": {
    "version": 7,
    "last_updated": "2026-01-14T10:30:00Z",
    "sections": {
      "plan": {
        "owner": "Planner",
        "readers": ["Executor", "Verifier"],
        "data": { }
      },
      "results": {
        "owner": "Executor",
        "readers": ["Verifier", "Orchestrator"],
        "data": { }
      }
    }
  }
}
```

**Rule: If state is not inspectable, it is not real.**

## Additional Resources

### Reference Files

For detailed specification patterns:
- **`references/discovery-questions.md`** - Complete discovery framework (memory, reliability, economics, tools, testing)
- **`references/spec-templates.md`** - Complete templates for common agent types (including Human-in-the-Loop)
- **`references/common-mistakes.md`** - Specification anti-patterns to avoid
- **`references/twelve-factor-agents.md`** - Quick reference for all 12 Factor Agents principles

### Related Skills

Before specifying agents:
- **mas-decision-gate** - Decide if multi-agent is needed (Factor 10: Small focused agents)

After specifying agents:
- **coordination-patterns** - Design how agents interact (Factors 3, 5/6, 8, 12)
- **production-readiness** - Add observability and governance (Factors 9, 11)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/markusdegen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
