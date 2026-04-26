---
name: stakeholder-update-composer
description: Use when preparing status communications for stakeholders. Use at reporting cadence or for ad-hoc updates. Produces audience-appropriate updates with progress, risks, decisions needed, and next steps.
metadata:
  author: ethical-ai-syndicate
---

# Stakeholder Update Composer

## Overview

Compose effective status communications tailored to different stakeholder audiences. The same project state requires different framing for executives, technical leads, and operational teams.

**Core principle:** Every stakeholder cares about something different. Lead with what matters to them, not what you did.

## When to Use

- Weekly/monthly status reports
- Preparing for steering committee
- Communicating risks or blockers
- Celebrating milestones
- Requesting decisions

## Audience Profiles

| Audience | Cares About | Communication Style | Detail Level |
|----------|-------------|---------------------|--------------|
| **Executive** | Business outcomes, risks, decisions | Headlines + implications | Minimal |
| **Steering Committee** | Progress vs plan, budget, risks | Structured, RAG status | Summary |
| **Technical Lead** | Architecture, blockers, dependencies | Direct, specific | High |
| **Product Team** | Features, timeline, scope changes | Visual, milestone-focused | Medium |
| **Operations** | Rollout, support needs, training | Practical, actionable | High |

## Output Format

```yaml
stakeholder_update:
  audience: "[Audience type]"
  date: "[YYYY-MM-DD]"
  period: "[Week N | Month | Sprint N]"
  
  tldr:
    one_liner: "[Single sentence summary]"
    status: "[On Track | At Risk | Blocked | Complete]"
  
  progress:
    summary: "[2-3 sentences on progress]"
    key_accomplishments:
      - "[Accomplishment 1]"
      - "[Accomplishment 2]"
    metrics:
      - metric: "[Name]"
        current: "[Value]"
        target: "[Value]"
        trend: "[↑ | → | ↓]"
  
  risks_and_issues:
    - item: "[Description]"
      type: "[Risk | Issue | Blocker]"
      severity: "[High | Medium | Low]"
      mitigation: "[Action being taken]"
      owner: "[Who]"
      escalation_needed: [true | false]
  
  decisions_needed:
    - decision: "[What needs to be decided]"
      context: "[Brief background]"
      options:
        - option: "[Option A]"
          pros: "[Benefits]"
          cons: "[Drawbacks]"
        - option: "[Option B]"
          pros: "[Benefits]"
          cons: "[Drawbacks]"
      recommendation: "[Recommended option]"
      deadline: "[When decision needed]"
  
  next_period:
    focus: "[Primary focus]"
    milestones:
      - "[Expected milestone 1]"
      - "[Expected milestone 2]"
    dependencies:
      - "[What we need from others]"
  
  appendix:
    detailed_metrics: "[Link or table]"
    supporting_docs: ["[Links]"]
```

## Audience-Specific Templates

### Executive Update

```markdown
# [Project Name] - Executive Summary
**Date:** [Date] | **Status:** 🟢 On Track

## Bottom Line
[One sentence: what executives need to know]

## Key Metrics
| Metric | Current | Target | Status |
|--------|---------|--------|--------|
| [Metric] | [Value] | [Value] | 🟢/🟡/🔴 |

## Decisions Needed
- **[Decision]**: [Options summary]. Recommend [X]. Need answer by [date].

## Top Risk
[One risk that could impact timeline/budget/outcomes]
→ Mitigation: [Action]

## Next 30 Days
[One sentence on upcoming focus]
```

### Steering Committee Update

```markdown
# [Project Name] - Steering Committee Report
**Period:** [Date range] | **Overall Status:** 🟢 On Track

## Status Summary
| Workstream | Status | Trend | Notes |
|------------|--------|-------|-------|
| [Stream 1] | 🟢 | → | [Brief note] |
| [Stream 2] | 🟡 | ↓ | [Brief note] |

## Progress vs Plan
- **Planned:** [What was planned]
- **Actual:** [What was achieved]
- **Variance:** [If any, explain]

## Budget Status
| Category | Allocated | Spent | Remaining |
|----------|-----------|-------|-----------|
| [Category] | $X | $Y | $Z |

## Risks & Issues (Top 3)
1. 🔴 **[Risk]** - [Impact]. Mitigation: [Action]. Owner: [Name].
2. 🟡 **[Risk]** - [Impact]. Mitigation: [Action]. Owner: [Name].
3. 🟡 **[Issue]** - [Impact]. Resolution: [Action]. Owner: [Name].

## Decisions Required
- [ ] **[Decision]** - [Context]. Deadline: [Date].

## Next Period Plan
- [Key activity 1]
- [Key activity 2]
- [Key milestone]
```

### Technical Lead Update

```markdown
# [Project Name] - Technical Status
**Sprint:** [N] | **Date:** [Date]

## Completed
- [Technical accomplishment with specifics]
- [Another accomplishment]

## In Progress
- [Work item] - [% complete], ETA [date]
- [Work item] - [Status], blocked by [X]

## Blockers
1. **[Blocker]** - Need [X] from [team]. Impact: [delays Y].

## Technical Debt
- Added: [New debt item]
- Addressed: [Resolved debt item]

## Dependencies
| Dependency | Owner | Status | Need By |
|------------|-------|--------|---------|
| [API X] | Team Y | 🟡 Pending | [Date] |

## Architecture Notes
[Any significant technical decisions or changes]

## Code Quality
- Test coverage: [X%]
- Build status: [Green/Red]
- Open bugs: [N]
```

## RAG Status Guidelines

| Status | Criteria | Action Required |
|--------|----------|-----------------|
| 🟢 **Green** | On track, no significant risks | Continue as planned |
| 🟡 **Amber** | At risk, mitigation in progress | Monitor closely, may need intervention |
| 🔴 **Red** | Off track, needs escalation | Immediate attention, decision needed |

### When to Change Status

**Green → Amber:**
- Schedule slips >1 week
- New significant risk identified
- Key dependency at risk
- Resource constraint emerging

**Amber → Red:**
- Schedule slips >2 weeks
- Critical blocker with no mitigation
- Budget overrun >10%
- Executive decision needed

## Communication Cadence

| Audience | Frequency | Format |
|----------|-----------|--------|
| Executive | Monthly or milestone | Email + optional deck |
| Steering Committee | Bi-weekly | Structured report |
| Technical Lead | Weekly or daily standup | Brief, bullet points |
| Product Team | Sprint-based | Demo + notes |
| Operations | As needed pre-rollout | Detailed documentation |

## Common Mistakes

| Mistake | Problem | Fix |
|---------|---------|-----|
| Same update for all audiences | Irrelevant info, misses concerns | Tailor to audience |
| Activity focus | "We did X, Y, Z" | Outcome focus: "X enabled Y" |
| Hiding problems | Surprises later, trust damage | Surface risks early |
| No decisions requested | Passive update, no action | Always ask for what you need |
| Too long | Won't be read | Executive: 1 page; Others: 2 max |
| Jargon with execs | Confusion, disengagement | Plain business language |

## Decision Request Framework

When escalating for decision:

```yaml
decision_request:
  decision_needed: "[Clearly state what needs deciding]"
  
  context:
    background: "[Why this decision is needed now]"
    constraints: "[What limits our options]"
  
  options:
    - name: "Option A: [Name]"
      description: "[What this means]"
      pros: ["[Pro 1]", "[Pro 2]"]
      cons: ["[Con 1]", "[Con 2]"]
      cost: "[Time/money/resources]"
      risk: "[What could go wrong]"
    
    - name: "Option B: [Name]"
      description: "[What this means]"
      pros: ["[Pro 1]", "[Pro 2]"]
      cons: ["[Con 1]", "[Con 2]"]
      cost: "[Time/money/resources]"
      risk: "[What could go wrong]"
  
  recommendation: "[Which option and why]"
  
  if_no_decision: "[What happens if we don't decide]"
  
  deadline: "[When we need the answer]"
```

## Quality Checklist

Before sending:

- [ ] Lead with what audience cares about
- [ ] Status is clear (RAG or equivalent)
- [ ] Accomplishments are outcomes, not activities
- [ ] Risks include mitigation plans
- [ ] Decisions have clear options and deadline
- [ ] Next steps are specific
- [ ] Length appropriate for audience
- [ ] Jargon removed or explained

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
