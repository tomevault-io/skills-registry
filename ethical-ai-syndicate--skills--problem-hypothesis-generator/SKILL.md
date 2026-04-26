---
name: problem-hypothesis-generator
description: Use when exploring problem spaces before solution design. Use after identifying opportunity area. Produces structured hypotheses, validation criteria, experiment designs, and decision frameworks.
metadata:
  author: ethical-ai-syndicate
---

# Problem Hypothesis Generator

## Overview

Structure problem space exploration before jumping to solutions. Generates testable hypotheses about user problems, validates assumptions systematically, and designs experiments to learn quickly.

**Core principle:** Fall in love with the problem, not the solution. A well-defined problem hypothesis prevents building the wrong thing.

## When to Use

- Starting discovery for new initiative
- Validating problem before roadmap commitment
- Challenging assumptions about user needs
- Designing research to inform solutions

## Output Format

```yaml
problem_exploration:
  opportunity_area: "[High-level area of focus]"
  exploration_date: "[YYYY-MM-DD]"
  
  problem_hypotheses:
    - id: "PH-01"
      hypothesis: "[Specific, falsifiable statement about the problem]"
      target_user: "[Who experiences this problem]"
      current_behavior: "[What they do today]"
      assumed_pain: "[What's frustrating or costly]"
      assumed_frequency: "[How often it occurs]"
      assumed_severity: "[Impact when it occurs]"
      confidence: "[High | Medium | Low]"
      evidence_for:
        - "[Supporting evidence]"
      evidence_against:
        - "[Contradicting evidence]"
      validation_status: "[Unvalidated | In Progress | Validated | Invalidated]"
  
  assumptions:
    critical:
      - assumption: "[Must be true for problem to matter]"
        status: "[True | False | Unknown]"
        validation_method: "[How to test]"
        risk_if_wrong: "[Impact on initiative]"
    
    important:
      - assumption: "[Should be true for solution to work]"
        status: "[True | False | Unknown]"
        validation_method: "[How to test]"
    
    nice_to_validate:
      - assumption: "[Would be good to know]"
        status: "[True | False | Unknown]"
  
  validation_plan:
    experiments:
      - id: "EXP-01"
        hypothesis_tested: "PH-01"
        method: "[Interview | Survey | Prototype | Analytics]"
        sample_size: "[N]"
        duration: "[Time]"
        success_criteria: "[What validates hypothesis]"
        failure_criteria: "[What invalidates hypothesis]"
        resources_needed: "[Effort/cost]"
        decision_enabled: "[What we can decide after]"
  
  decision_framework:
    if_validated:
      - hypothesis: "PH-01"
        next_step: "[Solution exploration, etc.]"
    if_invalidated:
      - hypothesis: "PH-01"
        next_step: "[Pivot, abandon, or reframe]"
    if_inconclusive:
      next_step: "[Additional research needed]"
  
  parking_lot:
    - "[Ideas or assumptions to explore later]"
```

## Problem Hypothesis Structure

### Strong Hypothesis Formula
```
We believe that [target user]
experiences [problem/pain]
when [situation/trigger]
because [root cause].

This matters because [business impact].
```

### Example
```
We believe that operations analysts at mid-size banks
experience frustration with trade confirmation matching
when volume exceeds 200 confirmations per day
because the manual matching process can't scale.

This matters because it causes settlement delays 
and requires overtime, costing ~$50K/analyst/year.
```

## Assumption Mapping

### Categorizing Assumptions
| Assumption Type | Definition | Priority |
|-----------------|------------|----------|
| **Critical** | If wrong, problem doesn't exist or isn't worth solving | Validate first |
| **Important** | If wrong, solution approach needs to change | Validate early |
| **Nice-to-validate** | Informs details but doesn't change direction | Validate opportunistically |

### Common Problem Assumptions
```yaml
common_assumptions:
  problem_exists:
    - "Users actually experience this pain"
    - "Pain is significant enough to motivate action"
    - "Pain occurs with enough frequency to matter"
  
  problem_addressable:
    - "The problem is solvable"
    - "Users would adopt a solution"
    - "Solving it is technically feasible"
  
  problem_worth_solving:
    - "Large enough market affected"
    - "Willingness to pay for solution"
    - "Aligned with our capabilities"
```

## Validation Methods

### Light-Touch Methods (Days)
| Method | Best For | Sample Size |
|--------|----------|-------------|
| Desk research | Market existence, competitor validation | N/A |
| Customer interviews | Problem validation, pain discovery | 5-10 |
| Customer advisory board | Quick directional input | 3-5 |
| Sales/CS feedback | Known pain points | 3-5 interviews |

### Medium-Touch Methods (Weeks)
| Method | Best For | Sample Size |
|--------|----------|-------------|
| Survey | Quantifying frequency, prioritization | 50-200 |
| Clickable prototype | Solution direction validation | 5-10 |
| Concierge MVP | Process validation | 3-5 |
| Analytics analysis | Behavioral validation | Existing users |

### High-Touch Methods (Months)
| Method | Best For | Sample Size |
|--------|----------|-------------|
| Beta program | Full solution validation | 20-50 |
| A/B test | Solution optimization | Statistically significant |
| Fake door test | Demand validation | 100+ impressions |

## Experiment Design

### Success/Failure Criteria
```yaml
experiment:
  id: "EXP-01"
  hypothesis: "Operations analysts spend >2 hours/day on manual matching"
  method: "Structured interviews with time diary"
  sample: "8 operations analysts at 4 banks"
  
  success_criteria:
    - "6/8 report >2 hours daily on matching"
    - "Average reported time >90 minutes"
  
  failure_criteria:
    - "<3/8 report >2 hours"
    - "Average <45 minutes"
  
  inconclusive_if:
    - "4-5/8 report >2 hours (need larger sample)"
    - "High variance in responses"
```

### Interview Question Framework
```yaml
interview_structure:
  opening: "[Warm-up, context setting]"
  
  current_state:
    - "Walk me through how you handle [task] today"
    - "What tools do you use?"
    - "How long does it typically take?"
  
  pain_exploration:
    - "What's the most frustrating part?"
    - "When does it go wrong?"
    - "What do you do when [problem situation]?"
  
  impact:
    - "What happens when it doesn't work well?"
    - "How does this affect your day/job/team?"
  
  alternatives:
    - "Have you tried any alternatives?"
    - "What would ideal look like?"
  
  closing: "[Thank, next steps]"
```

## Decision Framework

### Post-Validation Decisions
```yaml
decision_tree:
  hypothesis_validated:
    strong_signal:
      action: "Proceed to solution exploration"
      confidence: "High"
    
    moderate_signal:
      action: "Proceed with caution, continue learning"
      confidence: "Medium"
  
  hypothesis_invalidated:
    pivot_option:
      action: "Explore related problems identified in research"
      next: "Generate new hypotheses"
    
    abandon_option:
      action: "Not worth pursuing, document learnings"
      next: "Return to opportunity backlog"
  
  inconclusive:
    action: "Design additional experiments"
    options:
      - "Larger sample size"
      - "Different methodology"
      - "More specific hypothesis"
```

## Common Mistakes

| Mistake | Problem | Fix |
|---------|---------|-----|
| Jumping to solutions | Miss the real problem | Hypothesis before design |
| Confirmation bias | Only hear what you expect | Seek disconfirming evidence |
| Vague hypotheses | Not falsifiable | Specific, measurable claims |
| Interviewing wrong people | Invalid data | Define target precisely |
| Too long validation | Slow progress | Time-box experiments |
| Ignoring inconclusive | Wasted research | Define decision criteria upfront |

## Exploration Checklist

- [ ] Problem hypotheses are specific and falsifiable
- [ ] Target user clearly defined
- [ ] Assumptions categorized by criticality
- [ ] Critical assumptions have validation plan
- [ ] Experiments have clear success/failure criteria
- [ ] Decision framework defined before experimenting
- [ ] Time-boxed validation plan

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
