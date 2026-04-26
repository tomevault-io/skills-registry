---
name: product-vision-synthesizer
description: Use when defining or refining product vision. Use after initial stakeholder input. Produces vision statement, success metrics, strategic alignment documentation, and elevator pitch.
metadata:
  author: ethical-ai-syndicate
---

# Product Vision Synthesizer

## Overview

Articulate a clear, compelling product vision that aligns stakeholders and guides decision-making. Synthesizes inputs from strategy, market research, and user needs into actionable vision artifacts.

**Core principle:** A good vision inspires action and enables autonomous decision-making. If teams can't use it to make tradeoffs, it's too vague.

## When to Use

- Starting a new product or major initiative
- Aligning stakeholders on product direction
- Onboarding new team members
- Making scope or priority decisions
- Annual/quarterly vision refresh

## Output Format

```yaml
product_vision:
  product_name: "[Name]"
  version_date: "[YYYY-MM-DD]"
  
  vision_statement:
    one_liner: "[Single sentence vision]"
    expanded: "[2-3 sentence elaboration]"
  
  elevator_pitch:
    for: "[Target user]"
    who: "[Has this need/problem]"
    the: "[Product name]"
    is_a: "[Product category]"
    that: "[Key benefit]"
    unlike: "[Competitor/alternative]"
    our_product: "[Key differentiator]"
  
  target_users:
    primary:
      persona: "[Name]"
      description: "[Who they are]"
      goals: ["[Goal 1]", "[Goal 2]"]
      pain_points: ["[Pain 1]", "[Pain 2]"]
    secondary:
      - persona: "[Name]"
        relationship: "[How they interact with product]"
  
  problem_statement:
    current_state: "[How things work today]"
    pain_points: ["[Pain 1]", "[Pain 2]"]
    cost_of_problem: "[Quantified impact]"
    desired_state: "[How things should work]"
  
  value_proposition:
    functional: "[What the product does]"
    emotional: "[How it makes users feel]"
    economic: "[Business value delivered]"
  
  success_metrics:
    north_star:
      metric: "[Primary success metric]"
      current: "[Baseline]"
      target: "[Goal]"
      timeframe: "[When]"
    supporting:
      - metric: "[Metric name]"
        target: "[Value]"
        rationale: "[Why this metric]"
  
  strategic_alignment:
    company_strategy: "[How this supports company goals]"
    portfolio_fit: "[Relationship to other products]"
    competitive_positioning: "[Market position]"
  
  scope_boundaries:
    in_scope:
      - "[What we will do]"
    out_of_scope:
      - "[What we explicitly won't do]"
    future_consideration:
      - "[What we might do later]"
  
  key_assumptions:
    - assumption: "[What we believe to be true]"
      validation: "[How we'll verify]"
      risk_if_wrong: "[Impact if incorrect]"
  
  decision_principles:
    - principle: "[Guiding principle for tradeoffs]"
      example: "[How to apply it]"
```

## Vision Statement Framework

### The One-Liner Formula
```
[Product] enables [target user] to [achieve outcome] by [unique approach].
```

**Examples:**
- "Slack enables distributed teams to collaborate seamlessly by bringing all communication into searchable channels."
- "Stripe enables internet businesses to accept payments effortlessly by abstracting away payment infrastructure complexity."

### Vision Quality Checklist
| Attribute | Test | Example |
|-----------|------|---------|
| **Inspiring** | Does it motivate the team? | "Transform how..." vs "Build a tool that..." |
| **Clear** | Can anyone understand it? | No jargon or buzzwords |
| **Directional** | Does it guide decisions? | "Focus on speed" enables tradeoffs |
| **Achievable** | Is it realistic? | Ambitious but not fantasy |
| **Measurable** | Can you tell if you've achieved it? | Ties to success metrics |

## Elevator Pitch Template

The Geoffrey Moore template:

```
For [target customer]
Who [statement of need or opportunity]
The [product name]
Is a [product category]
That [key benefit, reason to buy]
Unlike [primary competitive alternative]
Our product [statement of primary differentiation]
```

**Example:**
> For B2B SaaS companies with 10-50 employees who lose 30% of customers in the first 90 days due to poor onboarding, ChurnBuster is a customer success platform that reduces early churn through personalized intervention. Unlike generic analytics tools, ChurnBuster combines behavioral signals with automated engagement workflows.

## Success Metrics Framework

### North Star Metric
The single metric that best captures value delivered:

| Product Type | Example North Star |
|--------------|-------------------|
| Marketplace | Transactions completed |
| SaaS | Weekly active users |
| Content | Time spent engaging |
| E-commerce | Purchase frequency |
| Productivity | Tasks completed |

### Supporting Metrics
```yaml
supporting_metrics:
  acquisition:
    - metric: "New user signups"
      target: "1,000/month"
  activation:
    - metric: "Complete onboarding"
      target: "60% of signups"
  engagement:
    - metric: "DAU/MAU ratio"
      target: ">25%"
  retention:
    - metric: "30-day retention"
      target: ">40%"
  revenue:
    - metric: "MRR growth"
      target: "10% month-over-month"
```

## Problem Statement Structure

### Current State
```yaml
current_state:
  who: "Operations analysts at mid-size banks"
  what_they_do: "Manually match trade confirmations to internal records"
  how_long: "2-3 hours per day"
  pain_points:
    - "Repetitive, low-value work"
    - "Error-prone manual process"
    - "Settlement delays from mismatches"
  cost:
    time: "1,500 hours/year per analyst"
    errors: "2% mismatch rate causing settlement fails"
    money: "$50K/year in failed settlement penalties"
```

### Desired State
```yaml
desired_state:
  experience: "Analysts review only exceptions, not every confirmation"
  outcome: "95% auto-match rate with human oversight"
  benefit: "Redirect analyst time to complex cases"
  measurement: "Same throughput with 60% less manual effort"
```

## Decision Principles

Help teams make autonomous decisions:

| Principle | Tradeoff Guidance |
|-----------|-------------------|
| "Speed over perfection" | Launch MVP, iterate later |
| "Enterprise-grade reliability" | Take time for testing, don't cut corners |
| "Self-service first" | Build features users can discover, minimize support |
| "Mobile-native experience" | Optimize for mobile even if web suffers |

**Applying Principles:**
```yaml
decision_principles:
  - principle: "Simplicity over feature richness"
    example: |
      When choosing between adding a complex feature 
      and improving an existing simple one, improve the existing.
    
  - principle: "Data-informed, not data-driven"
    example: |
      Metrics guide but don't dictate. User research 
      and judgment matter when data is inconclusive.
```

## Common Mistakes

| Mistake | Problem | Fix |
|---------|---------|-----|
| Too vague | "Make users happy" | Specific outcomes |
| Too narrow | Feature, not vision | Lift to outcome level |
| No boundaries | Everything is in scope | Explicit out-of-scope |
| No metrics | Can't measure success | Define north star |
| Buzzword-laden | "AI-powered synergy" | Plain language |
| Static | Never updated | Quarterly refresh |

## Vision Review Cadence

| Trigger | Action |
|---------|--------|
| Quarterly | Review metrics, adjust if needed |
| New strategy | Realign to company direction |
| Market shift | Assess competitive positioning |
| User feedback | Validate assumptions |
| Team confusion | Clarify and communicate |

## Output Checklist

- [ ] Vision statement is one inspiring sentence
- [ ] Elevator pitch follows template structure
- [ ] Target users are specific, not generic
- [ ] Problem is quantified with cost
- [ ] North star metric defined
- [ ] Strategic alignment documented
- [ ] Scope boundaries explicit
- [ ] Decision principles actionable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
