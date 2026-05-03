---
name: feature-orchestrator
description: This skill orchestrates complete feature development using specialized agents. Use when the user says "build feature", "implement feature", "develop feature end-to-end", "full feature lifecycle", "orchestrate development", or needs coordinated multi-agent feature work with discovery, design, implementation, and review phases. Use when this capability is needed.
metadata:
  author: codyrobertson
---

# Feature Development Orchestrator

Coordinate specialized agents through the complete feature development lifecycle: Discovery → Exploration → Architecture → Implementation → Review → Retrospective.

## Orchestration Philosophy

Feature development requires multiple perspectives working in concert. Rather than a single pass, orchestrate specialized agents that each bring domain expertise:

- **Explorer Agents** - Understand existing code patterns and architecture
- **Architect Agents** - Design solutions with different trade-offs
- **Implementation** - Build with continuous validation
- **Reviewer Agents** - Catch issues from security, performance, architecture angles
- **Retrospective Agent** - Evaluate delivery and identify improvements

## Phase 1: Discovery (Clarify Requirements)

Before any code exploration, establish clear requirements:

1. Identify the core problem being solved
2. List explicit and implicit requirements
3. Define success criteria
4. Surface constraints (performance, security, compatibility)
5. **Ask clarifying questions** - Do not proceed with ambiguity

Use the AskUserQuestion tool to gather missing requirements. Present questions in organized groups.

## Phase 2: Codebase Exploration (Parallel Agents)

Launch 2-3 code-explorer agents simultaneously using the Task tool:

```
Agent 1: "Find features similar to [feature] and trace implementation patterns"
Agent 2: "Map architecture and abstractions for [relevant area]"
Agent 3: "Analyze UI/API patterns and extension points"
```

Each agent should return:
- Key files to read (5-10 most important)
- Patterns discovered
- Integration points identified

After agents complete, **read all identified files** to build deep context.

## Phase 3: Architecture Design (Competing Approaches)

Launch 2-3 code-architect agents with different mandates:

```
Agent 1 (Minimal): "Design smallest change that solves the problem"
Agent 2 (Clean): "Design for maximum maintainability and elegance"
Agent 3 (Pragmatic): "Balance speed and quality for this context"
```

Synthesize approaches into:
- Trade-offs comparison table
- Your recommendation with reasoning
- Ask user which approach to pursue

## Phase 4: Implementation

Only proceed after explicit user approval of architecture.

1. Create detailed task list using TodoWrite
2. Read all relevant files before modifying
3. Implement following chosen architecture
4. Follow existing codebase conventions strictly
5. Update todos as progress is made
6. Commit logical chunks with clear messages

## Phase 5: Quality Review (Multi-Agent Critique)

Launch 3 specialized reviewer agents in parallel:

```
Security Agent: "Audit for OWASP Top 10, auth flaws, injection risks"
Performance Agent: "Identify O(n²) algorithms, N+1 queries, bottlenecks"
Architecture Agent: "Check SOLID violations, coupling, maintainability"
```

Invoke the progressive-analysis skill for comprehensive code critique:
```
Skill(skill="progressive-analysis")
```

Consolidate findings by severity:
- **Critical**: Must fix before merge
- **High**: Should fix this PR
- **Medium**: Fix soon
- **Low**: Technical debt backlog

Present findings to user and ask what to address.

## Phase 6: Sprint Retrospective

After implementation, invoke retrospective analysis:

```
Task(subagent_type="sprint-retrospective-analyst", prompt="Analyze the feature just implemented...")
```

The retrospective should cover:
- Goal alignment - Did we build what was needed?
- Code quality assessment
- Architecture evaluation
- Testing gaps identified
- Technical debt introduced
- Process improvements for next time

## Skill Invocation Patterns

To invoke other skills during orchestration:

```python
# For comprehensive code review
Skill(skill="progressive-analysis")

# For security-focused audit
Skill(skill="security-audit")

# For architecture review
Skill(skill="architecture-review")

# For brutal honest critique
Skill(skill="brutal-code-critique")

# For performance analysis
Skill(skill="performance-analysis")
```

## Agent Types for Task Tool

Use these specialized agents via Task tool:

| Agent Type | Use For |
|------------|---------|
| `Explore` | Quick codebase exploration |
| `feature-dev:code-explorer` | Deep feature tracing |
| `feature-dev:code-architect` | Architecture design |
| `feature-dev:code-reviewer` | Code review |
| `brutal-code-critic` | Comprehensive critique with fixes |
| `production-code-validator` | Pre-deployment validation |
| `sprint-retrospective-analyst` | Post-implementation review |
| `test-strategy-architect` | Test design and coverage |
| `security-audit` | Security vulnerability hunting |

## Parallel Execution Pattern

When launching multiple agents, use a single message with multiple Task tool calls:

```python
# Good: Parallel execution
<Task agent1>
<Task agent2>
<Task agent3>

# Bad: Sequential (slower)
<Task agent1>
# wait
<Task agent2>
# wait
<Task agent3>
```

## TodoWrite Integration

Track all phases with TodoWrite:

```python
TodoWrite(todos=[
    {"content": "Phase 1: Discovery", "status": "in_progress", "activeForm": "Clarifying requirements"},
    {"content": "Phase 2: Codebase Exploration", "status": "pending", "activeForm": "Exploring codebase"},
    {"content": "Phase 3: Architecture Design", "status": "pending", "activeForm": "Designing architecture"},
    {"content": "Phase 4: Implementation", "status": "pending", "activeForm": "Implementing feature"},
    {"content": "Phase 5: Quality Review", "status": "pending", "activeForm": "Reviewing code quality"},
    {"content": "Phase 6: Retrospective", "status": "pending", "activeForm": "Running retrospective"},
])
```

Update status as each phase completes. Never skip status updates.

## Output Format

At each phase transition, provide:

1. **Summary** of what was accomplished
2. **Key findings** or decisions made
3. **Next steps** with clear actions
4. **Questions** if any ambiguity exists

## When to Stop and Ask

Pause for user input when:
- Requirements are ambiguous
- Multiple valid architectures exist
- Trade-offs need user decision
- Implementation scope is unclear
- Review findings need prioritization

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codyrobertson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
