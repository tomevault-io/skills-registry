---
name: devflow-analytics
description: Track skill usage, agent performance, and thread outcomes. Provides insights into Dev Flow system effectiveness. Triggers on "analytics", "usage stats", "skill metrics", "performance report", "dev flow stats", "how am I using dev flow", "/analytics". Use when this capability is needed.
metadata:
  author: dorrianguy
---

# Dev Flow Analytics

Track and analyze how the Dev Flow system is being used. Provides metrics on skill invocations, agent performance, and thread outcomes.

## When to Use

- User asks about skill usage patterns
- User wants to see performance metrics
- Generating reports on Dev Flow effectiveness
- Identifying underutilized skills
- Tracking thread success rates
- Measuring productivity improvements

## Input Handling

**For Reports:**
- `period`: Time period (today, week, month, all)
- `focus`: Specific area (skills, agents, threads, all)
- `format`: Output format (summary, detailed, json)

**For Tracking (automatic):**
- Skill invocations are logged automatically
- Thread outcomes are captured on completion
- Agent delegations are recorded

## Instructions

### Data Collection

Analytics data is stored in `~/.claude/analytics/`:

```
~/.claude/analytics/
├── skill-usage.jsonl      # Skill invocation log
├── agent-metrics.json     # Agent performance summary
├── thread-history.jsonl   # Thread outcomes
└── daily-summary.json     # Rolling daily stats
```

### Skill Usage Schema (skill-usage.jsonl)

```json
{
  "timestamp": "2026-02-06T10:30:00Z",
  "skill": "launch-planner",
  "trigger": "build new feature",
  "duration_ms": 4500,
  "outcome": "success",
  "context": "Capsule Explore",
  "thread_id": "abc123"
}
```

### Agent Metrics Schema (agent-metrics.json)

```json
{
  "agents": {
    "implementer": {
      "tasks_completed": 45,
      "avg_duration_ms": 12000,
      "success_rate": 0.93,
      "common_tasks": ["code changes", "file edits", "refactoring"]
    },
    "code-reviewer": {
      "reviews_completed": 38,
      "ship_rate": 0.84,
      "avg_issues_found": 2.3
    }
  },
  "updated": "2026-02-06T10:30:00Z"
}
```

### Thread History Schema (thread-history.jsonl)

```json
{
  "thread_id": "abc123",
  "type": "b",
  "goal": "Add dark mode toggle",
  "started": "2026-02-06T10:00:00Z",
  "completed": "2026-02-06T10:45:00Z",
  "outcome": "shipped",
  "validation_iterations": 2,
  "skills_fired": ["launch-planner", "design-guide", "test-engineer"],
  "agents_used": ["implementer", "code-reviewer", "validator"]
}
```

### Report Generation

#### Summary Report (Default)

```markdown
## Dev Flow Analytics Report

**Period:** Last 7 days
**Generated:** 2026-02-06

### Overview

| Metric | Value | Trend |
|--------|-------|-------|
| Total Skill Invocations | 156 | ↑ 12% |
| Unique Skills Used | 24 | → 0% |
| Thread Success Rate | 89% | ↑ 5% |
| Avg Thread Duration | 18 min | ↓ 8% |

### Top Skills

| Rank | Skill | Invocations | Success Rate |
|------|-------|-------------|--------------|
| 1 | launch-planner | 34 | 100% |
| 2 | test-engineer | 28 | 96% |
| 3 | code-reviewer | 25 | 100% |
| 4 | design-guide | 18 | 94% |
| 5 | implementer | 15 | 93% |

### Underutilized Skills

These skills haven't been used recently:
- `competitive-intelligence` (14 days)
- `revenue-optimizer` (10 days)
- `accessibility-auditor` (7 days)

### Agent Performance

| Agent | Tasks | Success | Avg Duration |
|-------|-------|---------|--------------|
| implementer | 45 | 93% | 12s |
| code-reviewer | 38 | 100% | 8s |
| validator | 52 | 87% | 5s |

### Thread Outcomes

| Type | Count | Shipped | Avg Iterations |
|------|-------|---------|----------------|
| base | 28 | 89% | 1.2 |
| b (big) | 12 | 83% | 2.4 |
| p (parallel) | 5 | 100% | 1.0 |

### Recommendations

1. **Try `competitive-intelligence`** - Useful for market analysis
2. **Run accessibility audit** - Last check was 7 days ago
3. **Thread validation improving** - Down from 2.8 to 2.4 iterations
```

#### Detailed Report

Includes:
- Full skill invocation log
- Individual thread breakdowns
- Time-series charts (ASCII)
- Skill chain effectiveness

### Tracking Functions

**Log Skill Invocation:**
```javascript
// Called automatically by organism
logSkillInvocation({
  skill: "launch-planner",
  trigger: userMessage,
  startTime: Date.now()
});
```

**Log Thread Outcome:**
```javascript
// Called on thread completion
logThreadOutcome({
  threadId: currentThread.id,
  type: currentThread.type,
  outcome: "shipped", // or "abandoned", "blocked"
  iterations: validationCount
});
```

**Update Agent Metrics:**
```javascript
// Called after agent task
updateAgentMetrics({
  agent: "implementer",
  task: "code changes",
  duration: elapsed,
  success: true
});
```

## Required Output Sections

### Analytics Summary

```markdown
## Dev Flow Analytics

**Period:** {{period}}
**Skills Used:** {{unique_skills}} / {{total_skills}}
**Success Rate:** {{success_rate}}%

### Quick Stats
- Most used: {{top_skill}}
- Least used: {{bottom_skill}}
- Busiest day: {{busiest_day}}
- Total threads: {{thread_count}}
```

### Recommendations

```markdown
## Recommendations

Based on your usage patterns:

1. **{{recommendation_1}}**
   - Why: {{reason_1}}
   - Action: {{action_1}}

2. **{{recommendation_2}}**
   - Why: {{reason_2}}
   - Action: {{action_2}}
```

## Guardrails

- Never log sensitive data (secrets, passwords, PII)
- Aggregate metrics, don't store raw conversation content
- Respect user privacy - analytics are local only
- Allow users to clear analytics data
- Don't track during "quick:" prefixed requests

## Commands

### /analytics
```
/analytics                    # Summary for last 7 days
/analytics today              # Today's stats
/analytics month detailed     # Full month breakdown
/analytics skills             # Skill-focused report
/analytics threads            # Thread-focused report
/analytics clear              # Clear all analytics data
```

## Trigger Patterns

```javascript
if (/(analytics|usage stats|skill metrics|performance report|dev flow stats|how am I using|\\/analytics)/i.test(objective)) {
  // Run devflow-analytics
}
```

## Integration

**Dependencies:** None (standalone tracking)
**Triggers:** Runs passively, reports on demand
**Data Location:** `~/.claude/analytics/`

## Version History

- v1.0.0: Initial release with skill, agent, and thread tracking

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dorrianguy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
