---
name: ai-portfolio-optimizer
description: Use when balancing AI investments across initiatives. Use during planning cycles. Produces portfolio analysis, resource allocation, and investment recommendations.
metadata:
  author: ethical-ai-syndicate
---

# AI Portfolio Optimizer

## Overview

Optimize AI investments across the portfolio of initiatives. Balance innovation, efficiency, and maintenance investments to maximize overall value.

**Core principle:** Organizations have limited AI resources. Strategic allocation across a balanced portfolio delivers more value than ad-hoc investment.

## When to Use

- Annual/quarterly AI planning
- Resource allocation decisions
- Portfolio reviews
- Investment prioritization

## Output Format

```yaml
ai_portfolio:
  period: "[Planning period]"
  date: "[YYYY-MM-DD]"
  
  current_state:
    total_investment: "[$]"
    distribution:
      - category: "[Category]"
        allocation: "[%]"
        initiatives: "[N]"
    
    initiatives:
      - id: "[ID]"
        name: "[Name]"
        category: "[Innovation | Efficiency | Maintenance]"
        investment: "[$]"
        status: "[Active | Planned | Complete]"
        value_delivered: "[$]"
        health: "[Green | Amber | Red]"
  
  analysis:
    balance:
      current: "[Assessment of current balance]"
      recommendation: "[Target allocation]"
    
    value_realization:
      on_track: "[N initiatives]"
      at_risk: "[N initiatives]"
      underperforming: "[N initiatives]"
    
    resource_utilization:
      capacity: "[Available resources]"
      allocated: "[Committed]"
      available: "[Remaining]"
    
    gaps:
      - "[Strategic gap 1]"
  
  optimization:
    recommended_changes:
      - initiative: "[ID]"
        current: "[Current investment]"
        recommended: "[New investment]"
        rationale: "[Why]"
    
    new_investments:
      - opportunity: "[Description]"
        category: "[Category]"
        investment: "[$]"
        expected_value: "[$]"
    
    sunset_candidates:
      - initiative: "[ID]"
        reason: "[Why retire]"
        reallocation: "[Where to redirect]"
  
  target_portfolio:
    allocation:
      innovation: "[%]"
      efficiency: "[%]"
      maintenance: "[%]"
    
    initiatives:
      - id: "[ID]"
        investment: "[$]"
        priority: "[1-N]"
  
  recommendation:
    summary: "[Executive summary]"
    key_actions:
      - "[Action 1]"
```

## Portfolio Categories

### Innovation
```yaml
innovation:
  definition: "New AI capabilities, exploratory, higher risk"
  examples:
    - "New AI products"
    - "Novel use cases"
    - "Emerging technology exploration"
  
  characteristics:
    - "Higher uncertainty"
    - "Longer time to value"
    - "Potential for high returns"
    - "Learning opportunities"
  
  target_allocation: "20-30%"
```

### Efficiency
```yaml
efficiency:
  definition: "AI to improve existing processes and products"
  examples:
    - "Automation of manual tasks"
    - "Cost reduction initiatives"
    - "Productivity improvements"
  
  characteristics:
    - "Moderate risk"
    - "Measurable ROI"
    - "Builds on existing capabilities"
  
  target_allocation: "40-50%"
```

### Maintenance
```yaml
maintenance:
  definition: "Keep existing AI systems running and current"
  examples:
    - "Model retraining"
    - "Infrastructure updates"
    - "Technical debt"
    - "Security patches"
  
  characteristics:
    - "Lower risk"
    - "Necessary investment"
    - "Avoids degradation"
  
  target_allocation: "20-30%"
```

## Portfolio Balancing

### Healthy vs Unhealthy Portfolio
```yaml
healthy_signs:
  - "Balanced across categories"
  - "Pipeline of innovation feeding future"
  - "Efficiency initiatives delivering ROI"
  - "Maintenance preventing degradation"
  - "Resources aligned to priorities"

unhealthy_signs:
  - "All resources on maintenance (no innovation)"
  - "Only innovation (tech debt growing)"
  - "Scattered investments (no focus)"
  - "Over-committed resources"
```

### Rebalancing Triggers
| Trigger | Action |
|---------|--------|
| Innovation > 40% | More risk than sustainable |
| Maintenance > 40% | Starving future investment |
| Efficiency underperforming | Review or reallocate |
| Major strategy shift | Realign to new priorities |

## Prioritization Framework

### Scoring Criteria
```yaml
scoring:
  strategic_alignment:
    weight: 30%
    scale: "1-5"
  
  value_potential:
    weight: 25%
    scale: "ROI or $ value"
  
  feasibility:
    weight: 20%
    scale: "1-5 (data, tech, org readiness)"
  
  risk:
    weight: 15%
    scale: "1-5 (lower is less risky)"
  
  urgency:
    weight: 10%
    scale: "1-5 (competitive, regulatory drivers)"
```

### Priority Matrix
```
              High Strategic Alignment
                        │
    ┌───────────────────┼───────────────────┐
    │  Do eventually    │  Must do          │
    │  (lower priority) │  (highest priority)│
Low ├───────────────────┼───────────────────┤High
Value│  Don't do         │  Quick wins       │Value
    │  (deprioritize)   │  (do if capacity) │
    └───────────────────┼───────────────────┘
                        │
              Low Strategic Alignment
```

## Resource Optimization

### Capacity Planning
```yaml
capacity:
  roles:
    - role: "Data Scientist"
      available_fte: "[N]"
      allocated_fte: "[N]"
      utilization: "[%]"
    
    - role: "ML Engineer"
      available_fte: "[N]"
      allocated_fte: "[N]"
      utilization: "[%]"
  
  constraints:
    - "[Constraint 1]"
  
  recommendations:
    - "[Hire, reallocate, or defer based on gaps]"
```

## Portfolio Review Cadence

| Review | Frequency | Focus |
|--------|-----------|-------|
| Executive | Quarterly | Strategic alignment, major decisions |
| Portfolio | Monthly | Health, progress, risks |
| Initiative | Weekly | Execution, blockers |

## Checklist

- [ ] Current portfolio documented
- [ ] Initiatives categorized
- [ ] Value delivery assessed
- [ ] Balance analyzed
- [ ] Optimization opportunities identified
- [ ] Resource capacity considered
- [ ] Recommendations prioritized
- [ ] Stakeholder alignment

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
