---
name: research-planner
description: Create detailed research plans by decomposing structured prompts into subtopics, search strategies, and agent deployment configurations. Extracted from Phase 2 of research-executor for standalone planning capabilities. Use when this capability is needed.
metadata:
  author: yeheng
---

# Research Planner

## Overview

Takes a structured research prompt (from question-refiner) and creates a comprehensive execution plan with subtopic decomposition, search strategies, and multi-agent deployment configuration.

## When to Use

- User has structured prompt and wants to review plan before execution
- Need to estimate resources (agents, time, cost) for research
- Want to modify/approve plan before committing to execution
- Planning complex research requiring strategic review

## Architecture Position

```
question-refiner (structured prompt)
         ↓
   research-planner (this skill)
         ↓
   research-executor (validates & executes)
```

## Input Requirements

**Required**: Structured prompt with TASK, CONTEXT, SPECIFIC_QUESTIONS, KEYWORDS, CONSTRAINTS, OUTPUT_FORMAT

**Optional**: Complexity level, budget constraints, preferred agent types

## Output Structure

```markdown
# Research Plan: [Topic]

## 1. Executive Summary
- Topic, Research Type, Complexity
- Estimated Duration: [15-90 min]
- Estimated Cost: [$X]

## 2. Subtopic Decomposition
[3-7 subtopics with priority]

## 3. Search Strategies
[3-5 queries per subtopic]

## 4. Data Sources
| Source Type | Priority | Rationale |

## 5. Agent Deployment
- Total Agents: [3-8]
- Model Mix: [sonnet + haiku]
- Assignments per agent

## 6. Resource Estimation
| Resource | Estimate |
|----------|----------|
| Time | X min |
| Tokens | X |
| Agents | X |

## 7. Quality Gates
- Phase 3: ≥80% agent success
- Phase 5: ≥30 citations
- Final: Quality ≥8.0

## 8. Approval Options
✅ Approve | 🔧 Modify | 🔄 Alternative | ❌ Cancel
```

## Agent Deployment Matrix

| Research Type | Agents | Model Mix |
|---------------|--------|-----------|
| Quick Query | 2-3 | All haiku |
| Standard | 4-5 | 2 sonnet + 3 haiku |
| Deep Research | 6-8 | 3-4 sonnet + rest haiku |
| Technical | 3-5 | All sonnet |

## Resource Estimation

```
Time (min) = 15 + (subtopics × 5) + (agents × 3)
Tokens = agents × 15,000 + 10,000 (overhead)
```

## Plan Modification Support

Users can request:
- Add/remove subtopics
- Adjust agent count or model mix
- Change time/cost budget
- Modify search strategies

## Integration

**Upstream**: `question-refiner` (structured prompt)
**Downstream**: `research-executor` (execution plan)
**Parallel**: `ontology-scout` (domain reconnaissance)

---

**See also**: [Skill Base Template](../../shared/templates/skill_base_template.md)

## Examples

See [examples.md](./examples.md) for planning scenarios.

## Detailed Instructions

See [instructions.md](./instructions.md) for implementation guide.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yeheng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
