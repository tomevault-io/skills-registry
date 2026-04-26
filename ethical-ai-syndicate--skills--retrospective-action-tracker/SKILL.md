---
name: retrospective-action-tracker
description: Use when tracking retrospective commitments. Use after retro completed. Produces action item registry, progress tracking, and recurring pattern detection.
metadata:
  author: ethical-ai-syndicate
---

# Retrospective Action Tracker

## Overview

Track retrospective action items to completion and detect recurring patterns that indicate systemic issues. Ensure continuous improvement actually happens.

**Core principle:** Retrospective value comes from action, not discussion. Track, follow up, and close the loop.

## When to Use

- After sprint/project retrospectives
- Reviewing action item completion
- Identifying recurring themes
- Preparing for next retrospective

## Output Format

```yaml
retrospective_tracking:
  team: "[Team name]"
  period: "[Current quarter/sprint]"
  
  active_actions:
    - id: "ACT-001"
      source_retro: "[Sprint N | Project X]"
      date_identified: "[YYYY-MM-DD]"
      action: "[Specific action to take]"
      category: "[Process | Communication | Technical | Team]"
      owner: "[Person responsible]"
      due_date: "[YYYY-MM-DD]"
      status: "[Not Started | In Progress | Completed | Blocked | Cancelled]"
      progress_notes: "[Latest update]"
      days_open: "[N]"
      at_risk: [true | false]
  
  completed_actions:
    - id: "ACT-098"
      action: "[What was done]"
      closed_date: "[YYYY-MM-DD]"
      outcome: "[Result of action]"
      time_to_close: "[Days]"
  
  recurring_patterns:
    - pattern: "[Issue that keeps appearing]"
      occurrences:
        - retro: "[Sprint N]"
          manifestation: "[How it showed up]"
      root_cause_hypothesis: "[Why this keeps happening]"
      systemic_fix: "[What would address root cause]"
      escalation_needed: [true | false]
  
  metrics:
    total_actions_this_quarter: "[N]"
    completion_rate: "[%]"
    average_time_to_close: "[Days]"
    oldest_open_action: "[Days]"
    actions_at_risk: "[N]"
  
  retro_health:
    action_quality: "[Good | Mixed | Poor]"
    follow_through: "[Strong | Inconsistent | Weak]"
    recommendations: ["[Improvement suggestion]"]
```

## Action Item Quality

### SMART Actions
| Attribute | Good Example | Poor Example |
|-----------|--------------|--------------|
| **Specific** | "Add integration test for payment flow" | "Improve testing" |
| **Measurable** | "Reduce deploy time from 30 to 15 min" | "Deploy faster" |
| **Assigned** | "Owner: Sarah" | "Someone should..." |
| **Realistic** | "Complete by next sprint" | "Fix everything" |
| **Timebound** | "Due: Feb 15" | "Eventually" |

### Action Categories
| Category | Examples |
|----------|----------|
| **Process** | Standup format, PR review process, deployment workflow |
| **Communication** | Meeting structure, stakeholder updates, documentation |
| **Technical** | Tech debt, tooling, test coverage |
| **Team** | Pairing, skills development, workload balance |

## Status Tracking

### Status Definitions
| Status | Meaning | Action |
|--------|---------|--------|
| **Not Started** | Acknowledged but not begun | Check if blocked or needs reprioritizing |
| **In Progress** | Active work happening | Track progress toward completion |
| **Completed** | Done and verified | Capture outcome, celebrate |
| **Blocked** | Cannot proceed | Escalate or find workaround |
| **Cancelled** | No longer relevant | Document why, close |

### At-Risk Indicators
```yaml
at_risk_if:
  - "Open > 14 days without progress"
  - "Due date passed"
  - "Owner unavailable"
  - "Dependencies unresolved"
```

## Pattern Detection

### Identifying Recurring Issues
```yaml
pattern_analysis:
  pattern: "Sprint scope changes mid-sprint"
  occurrences:
    - sprint: "Sprint 12"
      details: "3 stories added after planning"
    - sprint: "Sprint 14"
      details: "Urgent bug consumed 30% capacity"
    - sprint: "Sprint 16"
      details: "Executive request bumped planned work"
  
  frequency: "Every 2-3 sprints"
  impact: "Missed commitments, team frustration"
  
  point_fixes_tried:
    - "Said no to additions (didn't stick)"
    - "Added buffer (still consumed)"
  
  root_cause: "No process to protect sprint scope"
  
  systemic_fix: |
    Implement change request process: 
    - Any mid-sprint addition requires removing equal points
    - PO has authority to reject or defer
    - Track and report scope changes
```

### Escalation Criteria
```yaml
escalate_when:
  - "Same issue 3+ times in 6 months"
  - "Point fixes don't work"
  - "Fix requires org-level change"
  - "Outside team's authority to resolve"
```

## Retro Health Metrics

### Tracking Effectiveness
```yaml
health_metrics:
  action_completion:
    target: ">80%"
    current: "65%"
    trend: "Declining"
    diagnosis: "Too many actions, insufficient focus"
  
  time_to_close:
    target: "<14 days"
    current: "21 days"
    trend: "Stable"
    diagnosis: "Actions too big, need smaller scope"
  
  action_quality:
    assessment: "Mixed"
    issues:
      - "40% of actions lack clear owner"
      - "Some actions aren't actionable"
```

### Improvement Recommendations
| Issue | Recommendation |
|-------|----------------|
| Low completion rate | Fewer, more focused actions (max 3 per retro) |
| Long time to close | Break into smaller actions |
| Vague actions | Apply SMART criteria strictly |
| Recurring issues | Root cause analysis, not point fixes |
| No follow-up | Start each retro with action review |

## Retro Facilitation Tips

### Action Generation
```yaml
good_practices:
  limit_actions: "Maximum 3 actions per retrospective"
  assign_immediately: "Owner volunteers before leaving room"
  size_appropriately: "Can complete in 1-2 sprints"
  track_visibly: "Action board visible to all"
```

### Review Cadence
```yaml
review_schedule:
  standup: "Quick status on any blocked actions"
  weekly: "Owner provides progress update"
  next_retro: "Open with action review before new discussion"
```

## Template: Action Item
```yaml
action:
  id: "[AUTO-GENERATED]"
  source: "[Retro name/date]"
  description: "[SMART description of what to do]"
  category: "[Process | Communication | Technical | Team]"
  owner: "[Name]"
  due_date: "[Date]"
  success_criteria: "[How we know it's done]"
  
  updates:
    - date: "[Date]"
      status: "[Status]"
      note: "[Progress or blocker]"
```

## Tracking Checklist

- [ ] All actions have clear owners
- [ ] All actions have due dates
- [ ] Progress reviewed weekly
- [ ] Blocked items escalated
- [ ] Completed items have outcomes documented
- [ ] Recurring patterns identified
- [ ] Systemic fixes proposed for patterns
- [ ] Metrics tracked and reported

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
