---
name: technical-debt-cataloger
description: Use when documenting and prioritizing technical debt. Use after development identifies debt. Produces debt registry with impact scoring, remediation cost, and payoff timing recommendations.
metadata:
  author: ethical-ai-syndicate
---

# Technical Debt Cataloger

## Overview

Systematically document, prioritize, and plan remediation of technical debt. Makes debt visible to stakeholders and enables informed trade-offs between features and debt paydown.

**Core principle:** Untracked debt is unmanaged debt. Make it visible, quantify the cost, and prioritize like any other work.

## When to Use

- New technical debt identified during development
- Quarterly debt review and planning
- Justifying debt remediation to stakeholders
- Balancing feature work with sustainability

## Output Format

```yaml
technical_debt_catalog:
  as_of_date: "[YYYY-MM-DD]"
  total_items: "[N]"
  estimated_total_remediation: "[Story points or days]"
  
  debt_items:
    - id: "DEBT-001"
      title: "[Short descriptive title]"
      description: "[What the debt is]"
      location: "[File/module/system affected]"
      
      classification:
        type: "[Code | Architecture | Test | Documentation | Infrastructure]"
        category: "[Deliberate | Accidental | Bit-rot | Outdated]"
        age: "[When introduced]"
      
      impact:
        velocity_tax: "[How it slows development]"
        risk_exposure: "[Security, reliability, scalability risk]"
        affected_areas: ["[Area 1]", "[Area 2]"]
        frequency_of_impact: "[Daily | Weekly | Monthly | Rare]"
      
      remediation:
        approach: "[How to fix]"
        estimated_effort: "[Story points or days]"
        complexity: "[Low | Medium | High]"
        prerequisites: ["[What must happen first]"]
        
      priority:
        impact_score: "[1-5]"
        urgency_score: "[1-5]"
        effort_score: "[1-5, lower is easier]"
        priority_score: "[Calculated]"
        recommended_timing: "[Now | Next Quarter | When Touched | Someday]"
      
      tracking:
        created_date: "[YYYY-MM-DD]"
        created_by: "[Name]"
        status: "[Open | Scheduled | In Progress | Resolved | Accepted]"
        scheduled_sprint: "[If planned]"
        resolved_date: "[If resolved]"
  
  summary:
    by_type:
      code: "[N items, X points]"
      architecture: "[N items, X points]"
      test: "[N items, X points]"
    
    by_priority:
      critical: "[N items]"
      high: "[N items]"
      medium: "[N items]"
      low: "[N items]"
    
    trends:
      new_this_quarter: "[N]"
      resolved_this_quarter: "[N]"
      net_change: "[+/- N]"
  
  recommendations:
    immediate_action:
      - "[Debt item to address now]"
    sprint_allocation: "[X% of capacity recommendation]"
    next_review_date: "[When to review again]"
```

## Debt Types

| Type | Description | Examples |
|------|-------------|----------|
| **Code** | Suboptimal code quality | Duplication, complex methods, poor naming |
| **Architecture** | Structural issues | Tight coupling, wrong abstractions, scaling limits |
| **Test** | Testing gaps | Low coverage, flaky tests, missing integration tests |
| **Documentation** | Knowledge gaps | Outdated docs, missing runbooks, tribal knowledge |
| **Infrastructure** | Platform issues | Outdated dependencies, manual processes, security gaps |

## Debt Categories

| Category | Description | Typical Response |
|----------|-------------|------------------|
| **Deliberate** | Consciously incurred for speed | Track and schedule paydown |
| **Accidental** | Unintentional, discovered later | Add to backlog, prioritize |
| **Bit-rot** | Degraded over time | Address when touching area |
| **Outdated** | Once-good decisions now obsolete | Plan migration |

## Impact Scoring

### Impact Score (1-5)
| Score | Velocity Impact | Risk Exposure |
|-------|----------------|---------------|
| 5 | Blocks major initiatives | Critical security/reliability risk |
| 4 | Significant slowdown | High risk if triggered |
| 3 | Noticeable friction | Moderate risk |
| 2 | Minor inconvenience | Low risk, contained |
| 1 | Minimal impact | Cosmetic or theoretical |

### Urgency Score (1-5)
| Score | Criteria |
|-------|----------|
| 5 | Getting worse rapidly, must address now |
| 4 | Will become critical within quarter |
| 3 | Stable but should address soon |
| 2 | Can wait, monitor for changes |
| 1 | No urgency, address opportunistically |

### Effort Score (1-5)
| Score | Effort |
|-------|--------|
| 1 | Hours, low risk |
| 2 | Days, straightforward |
| 3 | Sprint, some complexity |
| 4 | Multiple sprints, significant refactoring |
| 5 | Quarter+, major undertaking |

### Priority Calculation
```yaml
priority_formula: "(Impact × Urgency) / Effort"

example:
  impact: 4
  urgency: 3
  effort: 2
  priority_score: 6  # (4 × 3) / 2 = 6
  
interpretation:
  ">8": "Critical - address immediately"
  "5-8": "High - schedule in next sprint"
  "3-5": "Medium - plan for quarter"
  "<3": "Low - address when convenient"
```

## Remediation Timing

| Timing | When to Use |
|--------|-------------|
| **Now** | Critical priority, blocking work |
| **Next Quarter** | High priority, needs planning |
| **When Touched** | Medium priority, fix when in that code |
| **Someday** | Low priority, track but don't plan |
| **Accept** | Not worth fixing, document and move on |

## Debt Review Process

### Quarterly Review Agenda
```yaml
debt_review:
  attendees: "[Tech lead, PO, key engineers]"
  duration: "60-90 minutes"
  
  agenda:
    - item: "Review resolved debt"
      duration: "10 min"
      output: "Celebrate progress"
    
    - item: "Add new debt items"
      duration: "20 min"
      output: "Updated catalog"
    
    - item: "Re-score existing items"
      duration: "15 min"
      output: "Updated priorities"
    
    - item: "Decide next quarter focus"
      duration: "20 min"
      output: "Scheduled debt work"
    
    - item: "Capacity allocation"
      duration: "10 min"
      output: "% of sprints for debt"
```

### Capacity Allocation Guidelines
| Debt Health | Recommended Allocation |
|-------------|----------------------|
| Healthy (debt decreasing) | 10-15% |
| Stable (debt flat) | 15-20% |
| Growing (debt increasing) | 20-30% |
| Critical (blocking work) | 30%+ or dedicated sprint |

## Stakeholder Communication

### For Product/Business
```markdown
## Technical Debt Summary

**Current State:** [X] items, [Y] days estimated remediation

**Why It Matters:**
- [Item A] is slowing feature development by ~20%
- [Item B] poses security risk if not addressed

**Recommendation:**
- Allocate [X]% of sprint capacity to debt
- Prioritize [specific items] before [upcoming initiative]

**Trade-off:** Each sprint of debt work = [N] fewer features, but enables faster delivery long-term.
```

## Anti-Patterns

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| No catalog | Debt invisible | Track all known debt |
| Never prioritize | Everything equal | Score and rank |
| All or nothing | Wait for perfect fix | Incremental improvement |
| Shame-based | Blame for creating debt | Focus on resolution |
| Feature-only sprints | Debt grows indefinitely | Consistent allocation |

## Catalog Checklist

- [ ] All known debt items documented
- [ ] Impact and effort scored
- [ ] Priority calculated
- [ ] Remediation approach defined
- [ ] Quarterly review scheduled
- [ ] Capacity allocation agreed
- [ ] Stakeholders informed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
