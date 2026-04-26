---
name: roadmap-visualizer
description: Use when creating or updating product roadmaps. Use after prioritization complete. Produces timeline-based roadmap with themes, dependencies, milestones, and confidence indicators.
metadata:
  author: ethical-ai-syndicate
---

# Roadmap Visualizer

## Overview

Create clear, stakeholder-appropriate product roadmaps that communicate direction without false precision. Balance commitment with flexibility by using time horizons and confidence levels.

**Core principle:** A roadmap is a communication tool, not a project plan. It should show direction and intent, not Gantt-chart-level detail.

## When to Use

- Quarterly/annual planning
- Stakeholder alignment meetings
- Board/executive presentations
- Team direction-setting
- Customer conversations about future features

## Output Format

```yaml
roadmap:
  product: "[Product name]"
  version_date: "[YYYY-MM-DD]"
  planning_horizon: "[Quarters covered]"
  
  vision_context:
    vision: "[Product vision one-liner]"
    north_star: "[Primary success metric]"
    current_state: "[Where we are now]"
  
  themes:
    - id: "[THEME-01]"
      name: "[Theme name]"
      description: "[What this theme accomplishes]"
      strategic_goal: "[Which company goal this supports]"
      success_metric: "[How we measure theme success]"
  
  time_horizons:
    now:
      timeframe: "[Current quarter]"
      confidence: "High"
      items:
        - id: "[ITEM-01]"
          title: "[Initiative name]"
          theme: "[THEME-01]"
          description: "[Brief description]"
          status: "[In Progress | Planned | Complete]"
          dependencies: ["[Dependency]"]
          target_outcome: "[What success looks like]"
    
    next:
      timeframe: "[Next quarter]"
      confidence: "Medium"
      items:
        - id: "[ITEM-02]"
          title: "[Initiative name]"
          theme: "[THEME-01]"
          description: "[Brief description]"
          depends_on: ["[ITEM-01]"]
          open_questions: ["[What we need to learn]"]
    
    later:
      timeframe: "[Future quarters]"
      confidence: "Low"
      items:
        - id: "[ITEM-03]"
          title: "[Initiative name]"
          theme: "[THEME-02]"
          description: "[Brief description]"
          contingent_on: "[What must be true]"
  
  milestones:
    - date: "[YYYY-MM-DD]"
      name: "[Milestone name]"
      items: ["[ITEM-01]", "[ITEM-02]"]
      significance: "[Why this matters]"
  
  dependencies:
    internal:
      - item: "[ITEM-01]"
        depends_on: "[Team/System]"
        status: "[Confirmed | Pending | At-risk]"
    external:
      - item: "[ITEM-02]"
        depends_on: "[External party]"
        status: "[Confirmed | Pending | At-risk]"
        mitigation: "[If at-risk]"
  
  risks:
    - risk: "[Description]"
      impact: "[Which items affected]"
      mitigation: "[Planned response]"
  
  not_planned:
    - item: "[Thing we're NOT doing]"
      reason: "[Why]"
      reconsider_when: "[Trigger for revisiting]"
```

## Time Horizon Framework

Avoid false precision with dates. Use horizons:

| Horizon | Timeframe | Confidence | Detail Level |
|---------|-----------|------------|--------------|
| **Now** | Current quarter | High (>80%) | Specific features, committed |
| **Next** | Next quarter | Medium (50-80%) | Themes + key initiatives |
| **Later** | 6+ months | Low (<50%) | Directional themes only |
| **Not Planned** | N/A | N/A | Explicit exclusions |

### Confidence Indicators

| Visual | Meaning | Commitment Level |
|--------|---------|------------------|
| ██████ Solid | High confidence | Committed |
| ▓▓▓▓▓▓ Partial | Medium confidence | Planned |
| ░░░░░░ Light | Low confidence | Exploring |

## Theme-Based Organization

Group by strategic themes, not features:

### Example Themes
```yaml
themes:
  - id: "GROWTH"
    name: "New User Acquisition"
    description: "Reduce friction in signup and onboarding"
    
  - id: "RETENTION"
    name: "User Engagement"  
    description: "Increase stickiness and daily usage"
    
  - id: "PLATFORM"
    name: "Foundation & Scale"
    description: "Infrastructure for next growth phase"
    
  - id: "REVENUE"
    name: "Monetization"
    description: "New revenue streams and pricing"
```

## Stakeholder Views

Different audiences need different roadmap views:

### Executive View
```
┌─────────────────────────────────────────────────────────────┐
│ Product Roadmap 2026                                        │
├─────────────────────────────────────────────────────────────┤
│ Vision: Enable [outcome] for [users]                        │
│ North Star: [Metric] from X → Y                             │
├─────────────────────────────────────────────────────────────┤
│ Q1           │ Q2           │ Q3           │ Q4            │
│ ██████████   │ ▓▓▓▓▓▓▓▓▓   │ ░░░░░░░░░   │ ░░░░░░░░░    │
│ Growth       │ Retention    │ Platform     │ TBD          │
│ - Onboarding │ - Engagement │ - Scale      │              │
│ - Signup     │ - Mobile     │ - API        │              │
│              │              │              │              │
│ Milestone: 🎯 New user flow launch (Feb)                   │
└─────────────────────────────────────────────────────────────┘
```

### Team View
```yaml
# More detail for execution teams
now:
  - title: "Self-serve onboarding"
    description: "Users complete setup without support"
    epic_ids: ["EPIC-123", "EPIC-124"]
    owner: "Team Alpha"
    target: "Feb 15"
    progress: 60%
    blockers: ["API dependency with Platform team"]
```

### Customer View
```markdown
## Coming Soon
- **Improved onboarding** - Get started faster with guided setup
- **Mobile app** - Full functionality on iOS and Android

## Planned for Later This Year  
- **API access** - Build custom integrations
- **Advanced reporting** - Deeper analytics

_Timing is approximate and may change_
```

## Dependency Mapping

Visualize cross-team and external dependencies:

```
[Onboarding Flow] ──depends on──> [Platform: Auth API]
         │                              │
         │                              └──> Status: ✅ Confirmed
         │
         └──depends on──> [External: Payment Provider]
                                │
                                └──> Status: ⚠️ Pending contract
```

## Roadmap Anti-Patterns

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| Feature list | No strategy, just tasks | Organize by themes |
| Date-driven | False precision, missed dates | Use time horizons |
| Too detailed | Can't see forest for trees | Summary for execs |
| Never updated | Stale, ignored | Quarterly refresh |
| No "not doing" | Endless scope creep | Explicit out-of-scope |
| Hidden dependencies | Surprise blockers | Surface early |

## Update Cadence

| Trigger | Action |
|---------|--------|
| Quarterly | Full roadmap review and refresh |
| New strategy | Realign themes to goals |
| Major completion | Move items, update progress |
| Significant change | Communicate proactively |
| Stakeholder request | Prepare appropriate view |

## Roadmap Review Checklist

Before sharing:

- [ ] Aligned with current product vision
- [ ] Themes map to strategic goals
- [ ] Now/Next/Later clearly differentiated
- [ ] Confidence levels indicated
- [ ] Dependencies identified
- [ ] Risks surfaced
- [ ] "Not doing" is explicit
- [ ] Appropriate detail for audience
- [ ] Updated within last month

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
