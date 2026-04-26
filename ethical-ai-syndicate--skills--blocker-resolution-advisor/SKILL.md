---
name: blocker-resolution-advisor
description: Use when sprint blockers arise. Use after blocker identified. Produces resolution options, escalation paths, workaround recommendations, and communication templates.
metadata:
  author: ethical-ai-syndicate
---

# Blocker Resolution Advisor

## Overview

Quickly diagnose and resolve blockers that impede sprint progress. Provides structured analysis, resolution options, and escalation paths.

**Core principle:** Unblock the team, don't just document the block. Every day a blocker persists is compounded delay.

## When to Use

- Team identifies a blocker in standup
- Dependency is not delivered as expected
- Technical issue halts progress
- Resource constraint prevents work
- External party is unresponsive

## Output Format

```yaml
blocker_analysis:
  id: "BLOCK-001"
  reported_date: "[YYYY-MM-DD]"
  reported_by: "[Name]"
  
  description:
    what: "[Clear description of the blocker]"
    affected_work: ["[Story/task IDs]"]
    impact: "[What can't happen until resolved]"
    sprint_impact: "[How it affects sprint goal]"
  
  classification:
    type: "[Dependency | Technical | Resource | External | Decision]"
    severity: "[Critical | High | Medium]"
    urgency: "[Immediate | Today | This Week]"
  
  root_cause:
    hypothesis: "[Why this happened]"
    preventable: "[Yes/No]"
    prevention_for_future: "[What would prevent recurrence]"
  
  resolution_options:
    - option: "Option A"
      description: "[What to do]"
      effort: "[Time/cost]"
      risk: "[Potential issues]"
      owner: "[Who can execute]"
      timeframe: "[How long]"
      recommended: [true | false]
  
  workaround:
    available: [true | false]
    description: "[Temporary solution]"
    limitations: "[What's still not possible]"
    technical_debt: "[What we'll need to fix later]"
  
  escalation:
    required: [true | false]
    path:
      - level: 1
        who: "[Name/Role]"
        when: "[Condition for escalating]"
    already_escalated: [true | false]
    escalation_date: "[Date]"
  
  communication:
    stakeholders_to_notify: ["[Stakeholder]"]
    notification_template: "[What to say]"
  
  resolution:
    status: "[Open | In Progress | Resolved | Accepted]"
    selected_option: "[Option name]"
    resolution_date: "[Date]"
    actual_impact: "[Sprint impact after resolution]"
```

## Blocker Types

| Type | Description | Typical Resolution |
|------|-------------|-------------------|
| **Dependency** | Waiting on another team/system | Escalate, negotiate, or work around |
| **Technical** | Code/infrastructure issue | Debug, pair, or get expert help |
| **Resource** | Missing person/skill/tool | Reassign, hire, or borrow |
| **External** | Third party not responding | Alternative contact, escalate, or deadline |
| **Decision** | Awaiting approval/direction | Identify decision-maker, force decision |

## Severity Assessment

| Severity | Criteria | Response Time |
|----------|----------|---------------|
| **Critical** | Sprint goal at risk, no workaround | Immediate (hours) |
| **High** | Significant work blocked, limited workaround | Same day |
| **Medium** | Some work blocked, workaround exists | Within 2 days |

## Resolution Strategies

### Dependency Blockers
```yaml
strategies:
  - strategy: "Parallel work"
    when: "Independent work available"
    action: "Pull other stories, return when unblocked"
  
  - strategy: "Stub/mock"
    when: "Waiting for API/service"
    action: "Create mock, continue development"
  
  - strategy: "Escalation"
    when: "Dependency team unresponsive"
    action: "Escalate to management"
  
  - strategy: "Scope reduction"
    when: "Full dependency not needed"
    action: "Implement subset that doesn't require blocked item"
```

### Decision Blockers
```yaml
strategies:
  - strategy: "Propose default"
    when: "Low-risk decision"
    action: "Propose answer, proceed unless vetoed"
  
  - strategy: "Time-boxed decision"
    when: "Decision-maker available"
    action: "Schedule meeting, force decision"
  
  - strategy: "Escalate for authority"
    when: "No one will decide"
    action: "Escalate to find decision-maker"
```

## Escalation Framework

### When to Escalate
```yaml
escalate_if:
  - "Blocker >24 hours with no progress"
  - "Initial contact unresponsive"
  - "Blocker requires authority you don't have"
  - "Sprint goal at risk"
```

### Escalation Template
```markdown
**Subject:** [ESCALATION] Blocker on [Project] - Sprint Goal at Risk

**Situation:** [Brief description of blocker]

**Impact:** [What's blocked + sprint goal impact]

**Actions Taken:**
- [What you've already tried]
- [Who you've contacted]

**Request:** [Specific ask from escalation recipient]

**Timeline:** Need resolution by [date] to meet sprint goal.
```

## Communication Templates

### To Blocked Team Member
```markdown
"Confirmed, [Issue] is blocking your work. Here's what we're doing:
1. [Immediate action]
2. [Backup plan]
I'll update you by [time] or sooner if resolved."
```

### To Stakeholders
```markdown
"Heads up: [Story/feature] is blocked by [issue].
Impact: [How this affects timeline/scope]
Mitigation: [What we're doing about it]
I'll update you [frequency] until resolved."
```

## Workaround Assessment

### When to Use Workarounds
```yaml
workaround_acceptable_if:
  - "Unblocks significant work"
  - "Technical debt is bounded"
  - "Can be reversed when proper solution available"
  - "Timeline pressure justifies trade-off"

workaround_dangerous_if:
  - "Creates permanent technical debt"
  - "Introduces security or reliability risk"
  - "Makes real fix harder later"
  - "Masks underlying systemic issue"
```

## Prevention

### Post-Resolution Review
```yaml
prevention:
  root_cause: "[Why did this blocker occur]"
  could_prevent: [true | false]
  prevention_action: "[What would have avoided this]"
  systemic_issue: "[If this indicates larger problem]"
  add_to_retro: [true | false]
```

## Blocker Checklist

When blocker identified:
- [ ] Document clearly (what, impact, affected work)
- [ ] Classify (type, severity, urgency)
- [ ] Identify resolution options
- [ ] Assess workaround viability
- [ ] Assign owner for resolution
- [ ] Communicate to affected parties
- [ ] Track progress daily
- [ ] Escalate if no progress >24 hours
- [ ] Document resolution and prevention

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
