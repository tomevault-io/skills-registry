---
name: ai-workflow-integration
description: Convert AI fluency into throughput by embedding AI into repeatable workflows with triggers, quality gates, and iteration loops that compound over time. Use when this capability is needed.
metadata:
  author: leobessa
---

# Overview

**AI Workflow Integration** is Layer 6 of AI fluency—the ability to embed AI systematically into work processes. This converts individual AI interactions into repeatable, improving systems.

**Core Principle:** Convert fluency into throughput. One good prompt is a moment; a good workflow is a system.

**Fluency Signal:** Has documented workflows that improve AI outcomes over time.

---

## When to Use This Skill

- Designing repeatable AI-assisted processes
- When AI tasks are recurring but treated as one-offs
- When AI value isn't compounding over time
- When building AI into team workflows
- When transitioning from ad-hoc to systematic AI use

---

## Workflow Components

### The Five Elements

Every AI workflow needs:

#### 1. Trigger Conditions

**What initiates the workflow?**

Types of triggers:
- **Event-based**: "When a new support ticket arrives"
- **Schedule-based**: "Every Monday morning"
- **Threshold-based**: "When backlog exceeds 10 items"
- **Request-based**: "When user asks for X"

**Document:**
```markdown
TRIGGER: [What starts this workflow]
FREQUENCY: [How often it runs]
OWNER: [Who initiates]
```

#### 2. Input Specification

**What goes into the AI step?**

Specify:
- Required inputs (what must be present)
- Optional inputs (what improves output)
- Input format (structure, length, type)
- Input sources (where data comes from)

**Example:**
```markdown
INPUTS:
- Required: Customer email text, account history summary
- Optional: Previous interaction notes, sentiment score
- Format: Plain text, max 2000 words
- Source: CRM export, support queue
```

#### 3. AI Step Definition

**What does AI do?**

Using RSFDA from [ai-instruction-design](../ai-instruction-design/SKILL.md):
- Role the AI takes
- Scope of the task
- Format of output
- Decision rules
- Abstraction level

#### 4. Quality Gates

**How do you verify output?**

Gate types:
- **Automated**: Format checks, length limits, required fields
- **Human review**: Spot checks, approval workflows
- **Verification**: Fact-checking, source confirmation

**Define:**
```markdown
QUALITY GATES:
□ Gate 1: [Check] → [Pass/Fail criteria]
□ Gate 2: [Check] → [Pass/Fail criteria]
□ Gate 3: [Check] → [Pass/Fail criteria]

If fail: [Action - revise/escalate/reject]
```

#### 5. Output Handling

**What happens to the result?**

Specify:
- Where output goes
- Who receives it
- What actions follow
- How it's stored

---

## Workflow Patterns

### Pattern 1: Draft-Review-Publish

```
[Trigger] → [AI Draft] → [Human Review] → [Revise/Approve] → [Publish]
```

**Use for:** Content creation, documentation, communications

**Example:**
```markdown
WORKFLOW: Weekly Newsletter Draft

TRIGGER: Monday 9am
INPUT: Week's activity log, metrics dashboard
AI STEP: Draft newsletter summary (500 words, informal tone)
QUALITY GATE: Editor reviews for accuracy and tone
OUTPUT: Approved draft to publication queue
```

### Pattern 2: Analyze-Recommend-Decide

```
[Trigger] → [AI Analysis] → [AI Recommendations] → [Human Decision] → [Action]
```

**Use for:** Decision support, prioritization, resource allocation

**Example:**
```markdown
WORKFLOW: Bug Triage

TRIGGER: New bug report filed
INPUT: Bug description, stack trace, user context
AI STEP: Classify severity, suggest assignee, estimate effort
QUALITY GATE: Tech lead validates classification
OUTPUT: Triaged ticket in sprint backlog
```

### Pattern 3: Transform-Validate-Deliver

```
[Trigger] → [AI Transform] → [Validation] → [Delivery]
```

**Use for:** Data processing, format conversion, summarization

**Example:**
```markdown
WORKFLOW: Meeting Notes Processing

TRIGGER: Meeting transcript uploaded
INPUT: Raw transcript, attendee list, agenda
AI STEP: Extract action items, decisions, key points
QUALITY GATE: Verify all speakers identified, actions have owners
OUTPUT: Structured notes to team channel
```

### Pattern 4: Monitor-Alert-Respond

```
[Continuous Monitor] → [AI Detection] → [Alert] → [Response Workflow]
```

**Use for:** Anomaly detection, compliance monitoring, quality assurance

---

## Workflow Documentation Template

```markdown
# [Workflow Name]

## Overview
[One sentence: what this workflow does]

## Trigger
- Condition: [What starts the workflow]
- Frequency: [How often]
- Owner: [Who initiates]

## Inputs
| Input | Required | Source | Format |
|-------|----------|--------|--------|
| [Input 1] | Yes/No | [Source] | [Format] |

## AI Step
**Role:** [AI role]
**Task:** [What AI does]
**Output Format:** [Expected structure]

### Prompt Template
```
[The actual prompt used, with placeholders]
```

## Quality Gates
1. [Gate 1]: [Criteria]
2. [Gate 2]: [Criteria]
3. [Gate 3]: [Criteria]

## Output
- Destination: [Where output goes]
- Format: [Output format]
- Next action: [What happens next]

## Failure Handling
- If [failure condition]: [Response]

## Metrics
- Success rate: [Target]
- Time to complete: [Target]
- Revision rate: [Target]

## Version History
- v1.0: [Date] - Initial workflow
```

---

## Iteration and Improvement

### Capture Workflow Performance

Track for each workflow:
- **Success rate**: Outputs accepted without revision
- **Revision rate**: How often outputs need rework
- **Time savings**: Compared to fully manual process
- **Quality scores**: Based on downstream feedback

### Systematic Improvement

```markdown
WORKFLOW REVIEW (Monthly):

1. Review metrics vs targets
2. Identify patterns in failures
3. Analyze revision requests
4. Update prompt templates
5. Adjust quality gates
6. Document changes

Improvement log:
- [Date]: [Change] → [Result]
```

### Prompt Template Evolution

Workflows should include versioned prompts:

```markdown
PROMPT TEMPLATE v2.3

Changes from v2.2:
- Added constraint: exclude competitor mentions
- Clarified format: bullet points not paragraphs
- Added decision rule: flag items over $10K

[Template content...]
```

---

## Practices

### Workflow Audit

For existing AI use, document:

1. What AI tasks happen regularly?
2. What triggers them?
3. What inputs do they need?
4. How is quality verified?
5. Where do outputs go?

Convert findings into formal workflows.

### Failure Mode Analysis

For each workflow, identify:

1. What could go wrong at each step?
2. How would you detect it?
3. What's the fallback?

Document in the workflow specification.

### Metric Dashboard

Create visibility:

```markdown
WORKFLOW METRICS - [Time Period]

| Workflow | Runs | Success | Revisions | Avg Time |
|----------|------|---------|-----------|----------|
| [Name 1] | [N]  | [%]     | [%]       | [Time]   |
| [Name 2] | [N]  | [%]     | [%]       | [Time]   |

Trends: [Commentary on patterns]
Actions: [Improvements to make]
```

---

## Assessment Criteria

**Layer 6 Complete When:**
- [ ] Has documented 3+ repeatable AI workflows
- [ ] Workflows include all five elements
- [ ] Quality gates are defined and used
- [ ] Tracks workflow performance metrics
- [ ] Has improved workflows based on data

---

## Common Integration Failures

### Failure 1: Ad-Hoc Remains Ad-Hoc

**Wrong:** Doing the same AI task repeatedly without standardization
**Right:** Document, templatize, and improve recurring tasks

### Failure 2: No Quality Gates

**Wrong:** AI output goes directly to use without verification
**Right:** Every workflow has explicit quality checkpoints

### Failure 3: No Improvement Loop

**Wrong:** Same prompt used forever regardless of results
**Right:** Systematic review and iteration on workflows

### Failure 4: Missing Failure Handling

**Wrong:** Workflow breaks when AI produces bad output
**Right:** Defined fallbacks and escalation paths

---

## Related Skills

- [ai-instruction-design](../ai-instruction-design/SKILL.md) — Prompt templates within workflows
- [ai-evaluation-verification](../ai-evaluation-verification/SKILL.md) — Quality gate implementation
- [ai-system-governance](../ai-system-governance/SKILL.md) — Scaling workflows to teams

---

## Learn More

- [Workflow Templates](references/workflow-templates.md)
- [Metrics Dashboard Examples](references/metrics-examples.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/leobessa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
