---
name: okr-definition-assistant
description: Use when setting quarterly or annual objectives. Use after strategy alignment. Produces measurable OKRs with key results, baselines, tracking methodology, and cascade structure.
metadata:
  author: ethical-ai-syndicate
---

# OKR Definition Assistant

## Overview

Create well-structured Objectives and Key Results (OKRs) that drive focus, alignment, and measurable outcomes. Ensures objectives are inspiring and key results are specific, measurable, and ambitious.

**Core principle:** Objectives describe where you want to go. Key Results tell you if you're getting there.

## When to Use

- Quarterly/annual planning cycles
- Aligning team goals to company strategy
- Breaking down high-level goals into measurable outcomes
- Reviewing and refining existing OKRs

## Output Format

```yaml
okr_set:
  period: "[Q1 2026 | FY2026]"
  owner: "[Team or Individual]"
  parent_okr: "[Company/Department OKR this supports]"
  
  objectives:
    - id: "O1"
      objective: "[Qualitative, inspiring goal]"
      rationale: "[Why this matters now]"
      
      key_results:
        - id: "KR1.1"
          key_result: "[Specific, measurable outcome]"
          metric: "[What we're measuring]"
          baseline: "[Current value]"
          target: "[Target value]"
          stretch_target: "[Aspirational value]"
          measurement_method: "[How we'll measure]"
          tracking_frequency: "[Weekly | Monthly]"
          owner: "[Person responsible]"
          confidence: "[High | Medium | Low]"
          initiatives: ["[Related project or effort]"]
        
        - id: "KR1.2"
          key_result: "[Another measurable outcome]"
          # ... same structure
      
      dependencies:
        - "[What must be true for success]"
      
      risks:
        - risk: "[What could prevent achievement]"
          mitigation: "[How we'll address]"
  
  initiatives:
    - id: "INIT-01"
      name: "[Project/effort name]"
      supports: ["KR1.1", "KR2.3"]
      owner: "[Owner]"
      timeline: "[Start - End]"
  
  grading_criteria:
    score_definitions:
      "0.0": "No progress"
      "0.3": "Made some progress"
      "0.5": "Achieved about half"
      "0.7": "Achieved target"
      "1.0": "Achieved stretch target"
```

## OKR Structure

### Objectives: The Qualitative Goal
| Attribute | Description | Example |
|-----------|-------------|---------|
| **Inspirational** | Makes people want to achieve it | "Delight enterprise customers" |
| **Qualitative** | Direction, not number | Not "Increase NPS to 50" |
| **Action-oriented** | Implies movement | "Build", "Create", "Transform" |
| **Timebound** | Achievable in period | Not a multi-year vision |

**Good Objectives:**
- "Make our platform the fastest in the industry"
- "Build a world-class customer onboarding experience"
- "Establish our team as the go-to AI experts"

**Bad Objectives:**
- "Increase revenue by 20%" (This is a key result)
- "Keep doing what we're doing" (Not inspiring)
- "Build feature X" (Output, not outcome)

### Key Results: The Measurable Outcomes
| Attribute | Description | Example |
|-----------|-------------|---------|
| **Specific** | Clear what success looks like | "Reduce p95 latency to <100ms" |
| **Measurable** | Quantifiable with data | "Increase conversion from 5% to 8%" |
| **Achievable** | Possible but ambitious | 70% probability of success |
| **Relevant** | Proves objective progress | Tied to objective intent |
| **Timebound** | Deliverable in period | End of quarter |

**Key Result Formulas:**
```
Increase/Decrease [metric] from [X] to [Y]
Achieve [milestone] by [date]
Launch [capability] with [success criteria]
[X]% of [population] does [action]
```

**Good Key Results:**
- "Increase trial-to-paid conversion from 12% to 18%"
- "Reduce customer onboarding time from 14 days to 3 days"
- "Achieve Net Promoter Score of 50 (up from 32)"

**Bad Key Results:**
- "Implement new checkout flow" (Output, not outcome)
- "Improve customer satisfaction" (Not measurable)
- "100% of KPIs green" (Meta-goal, not result)

## OKR Quality Checklist

### Objective Quality
- [ ] Answers "What do we want to achieve?"
- [ ] Is qualitative and inspirational
- [ ] Clear enough to understand, vague enough to adapt
- [ ] Achievable within the period
- [ ] Doesn't contain numbers

### Key Result Quality
- [ ] Answers "How will we know if we achieved it?"
- [ ] Has a specific number or milestone
- [ ] Has a baseline (where we are now)
- [ ] Has a target (where we want to be)
- [ ] Can be measured objectively
- [ ] Is an outcome, not an output/activity
- [ ] 2-5 key results per objective

## Cascade Structure

Align OKRs vertically and horizontally:

```
Company Objective: "Become the market leader in our space"
    │
    ├── Company KR: "Increase market share from 15% to 25%"
    │         │
    │         ├── Sales O: "Dominate enterprise segment"
    │         │      └── Sales KR: "Close 15 enterprise deals >$500K"
    │         │
    │         └── Product O: "Build unmatched product capability"
    │                └── Product KR: "Launch 3 industry-first features"
    │
    └── Company KR: "Achieve #1 analyst ranking"
              │
              └── Marketing O: "Establish thought leadership"
                     └── Marketing KR: "10 executive speaking slots at tier-1 events"
```

## Scoring and Grading

### Score Methodology
| Score | Meaning | Interpretation |
|-------|---------|----------------|
| 0.0 - 0.3 | Red | Significant miss, requires retrospective |
| 0.4 - 0.6 | Yellow | Partial achievement, learning opportunity |
| 0.7 - 0.9 | Green | Achieved target, well executed |
| 1.0 | Blue | Exceeded stretch, exceptional performance |

**Note:** Consistent 1.0 scores suggest targets aren't ambitious enough.

### Expected Score Distribution
- Average team OKR score: 0.6 - 0.7
- If always 1.0: Set harder targets
- If always <0.4: Targets unrealistic or deprioritized

## Common Mistakes

| Mistake | Problem | Fix |
|---------|---------|-----|
| KRs are tasks | "Launch feature X" | Focus on outcome of launch |
| Too many OKRs | Loss of focus | 3-5 objectives, 2-5 KRs each |
| No baseline | Can't measure progress | Always state "from X to Y" |
| Sandbagging | Easy targets don't inspire | 70% confidence = right difficulty |
| Binary KRs | "Ship project" (0 or 1) | Add quality dimension |
| Business as usual | Not driving change | OKRs are for improvement |
| No owner | Diffused responsibility | Single DRI per KR |

## OKR vs KPI

| Aspect | OKR | KPI |
|--------|-----|-----|
| Purpose | Drive change | Monitor health |
| Timeframe | Quarterly/Annual | Ongoing |
| Ambition | Stretch (70% achievable) | Realistic |
| Example | "Increase NPS from 32 to 50" | "Maintain NPS >30" |

**Use OKRs for:** What you want to improve
**Use KPIs for:** What you want to maintain

## Review Cadence

| Cadence | Activity |
|---------|----------|
| Weekly | Check-in on progress, surface blockers |
| Monthly | Score KRs, assess trajectory |
| End of period | Final scoring, retrospective |
| Start of period | Set new OKRs, cascade alignment |

## OKR Template Examples

### Product Team
```yaml
objectives:
  - id: "O1"
    objective: "Deliver an onboarding experience users love"
    key_results:
      - key_result: "Increase onboarding completion from 45% to 75%"
      - key_result: "Reduce time-to-first-value from 7 days to 1 day"
      - key_result: "Achieve onboarding NPS of 60 (up from 35)"
```

### Engineering Team
```yaml
objectives:
  - id: "O1"
    objective: "Build a platform that scales with confidence"
    key_results:
      - key_result: "Reduce p99 API latency from 800ms to 200ms"
      - key_result: "Achieve 99.95% uptime (up from 99.5%)"
      - key_result: "Zero critical incidents caused by deployments"
```

### Sales Team
```yaml
objectives:
  - id: "O1"
    objective: "Establish dominance in the enterprise segment"
    key_results:
      - key_result: "Close 10 enterprise deals >$500K (up from 3)"
      - key_result: "Reduce enterprise sales cycle from 120 to 60 days"
      - key_result: "Achieve 85% enterprise renewal rate"
```

## Review Checklist

Before finalizing:

- [ ] 3-5 objectives maximum
- [ ] 2-5 key results per objective
- [ ] Every KR has baseline and target
- [ ] Targets are ambitious (70% confidence)
- [ ] Outcomes, not outputs
- [ ] Clear ownership assigned
- [ ] Measurement method defined
- [ ] Aligned with parent OKRs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
