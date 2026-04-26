---
name: feature-impact-analyzer
description: Use when evaluating shipped feature performance. Use after feature live for measurement period. Produces adoption metrics, outcome achievement assessment, and iteration recommendations.
metadata:
  author: ethical-ai-syndicate
---

# Feature Impact Analyzer

## Overview

Evaluate whether shipped features are delivering expected value. Close the feedback loop from "we shipped it" to "it worked" with structured impact analysis and iteration recommendations.

**Core principle:** Shipping is not success. Impact is success. Measure outcomes, not outputs.

## When to Use

- Feature has been live for measurement period (typically 2-4 weeks)
- Quarterly feature portfolio review
- Deciding whether to iterate, expand, or deprecate
- Building case studies for similar initiatives

## Output Format

```yaml
feature_impact:
  feature_name: "[Feature name]"
  ship_date: "[YYYY-MM-DD]"
  analysis_date: "[YYYY-MM-DD]"
  measurement_period: "[Duration since launch]"
  
  original_hypothesis:
    problem_addressed: "[What problem we solved]"
    target_outcome: "[Expected improvement]"
    success_metrics:
      - metric: "[Metric name]"
        baseline: "[Pre-launch value]"
        target: "[Expected value]"
  
  adoption_metrics:
    awareness:
      eligible_users: "[N]"
      users_aware: "[N]"
      awareness_rate: "[%]"
    
    trial:
      users_tried: "[N]"
      trial_rate: "[% of aware]"
    
    active_use:
      regular_users: "[N]"
      adoption_rate: "[% of eligible]"
      usage_frequency: "[Daily | Weekly | Monthly]"
    
    retention:
      still_using_after_30_days: "[%]"
  
  outcome_metrics:
    primary:
      - metric: "[Primary success metric]"
        baseline: "[Pre-launch]"
        target: "[Goal]"
        actual: "[Measured]"
        achievement: "[% of target]"
        verdict: "[Exceeded | Met | Partial | Missed]"
    
    secondary:
      - metric: "[Secondary metric]"
        baseline: "[Pre-launch]"
        actual: "[Measured]"
        change: "[+/- %]"
    
    unintended:
      positive:
        - "[Unexpected positive outcome]"
      negative:
        - "[Unexpected negative outcome]"
  
  segment_analysis:
    - segment: "[User segment]"
      adoption: "[Higher | Average | Lower than overall]"
      outcome_impact: "[Better | Average | Worse]"
      insights: "[Why this segment differs]"
  
  qualitative_feedback:
    sentiment: "[Positive | Mixed | Negative]"
    themes:
      positive:
        - "[What users like]"
      negative:
        - "[What users dislike]"
      requests:
        - "[What users want added]"
  
  operational_impact:
    support_volume:
      before: "[Tickets/period]"
      after: "[Tickets/period]"
      change: "[%]"
    
    performance:
      latency_impact: "[Change in response time]"
      error_rate_impact: "[Change in errors]"
  
  verdict:
    overall: "[Success | Partial Success | Underperforming | Failure]"
    recommendation: "[Expand | Iterate | Maintain | Deprecate]"
    rationale: "[Why this recommendation]"
  
  next_steps:
    if_expanding:
      - "[What to expand]"
    if_iterating:
      - "[What to change]"
    if_deprecating:
      - "[Migration plan]"
  
  learnings:
    - "[What we learned for future features]"
```

## Adoption Funnel

Track feature adoption through stages:

```
┌─────────────────────────────────────────────–──────┐
│ Eligible Users                          10,000    │
├───────────────────────────────────────────────────┤
│ ├── Aware (saw announcement/UI)          7,500    │ 75%
│ │   ├── Tried (used at least once)       3,000    │ 40%
│ │   │   ├── Adopted (regular use)        1,200    │ 40%
│ │   │   │   └── Retained (30-day)          900    │ 75%
└───────────────────────────────────────────────────┘

Adoption Rate = Adopted / Eligible = 12%
Trial-to-Adoption = Adopted / Tried = 40%
```

### Funnel Drop-off Analysis
| Drop-off Point | Indicates | Action |
|----------------|-----------|--------|
| Low awareness | Announcement failure | Better communication |
| Low trial | Poor discoverability | UX improvements |
| Low adoption | Feature doesn't solve problem | Iterate or pivot |
| Low retention | Value doesn't persist | Understand why stopped |

## Outcome Measurement

### Primary Metric Analysis
```yaml
primary_metric_analysis:
  metric: "Time to complete task X"
  baseline: "12 minutes"
  target: "6 minutes (-50%)"
  actual: "8 minutes (-33%)"
  
  assessment:
    target_met: false
    improvement_achieved: true
    gap_analysis: |
      Achieved 33% improvement vs 50% target. 
      Analysis shows remaining time in steps 
      not addressed by feature.
  
  recommended_action: |
    Iterate to address remaining bottleneck 
    in step 3 of workflow. Estimated additional 
    2-minute reduction possible.
```

### Statistical Significance
```yaml
significance:
  metric: "Conversion rate"
  sample_size: 5000
  baseline: "3.2%"
  measured: "3.8%"
  lift: "+18.8%"
  
  statistical_test: "Chi-square"
  p_value: 0.023
  confidence_level: "95%"
  significant: true
```

## Verdict Framework

### Success Categories
| Verdict | Criteria | Typical Action |
|---------|----------|----------------|
| **Success** | Primary metric exceeded, strong adoption | Expand, communicate wins |
| **Partial Success** | Some metrics met, others missed | Iterate on gaps |
| **Underperforming** | Low adoption despite feature working | Investigate why, possibly pivot |
| **Failure** | Primary metric missed, low adoption | Deprecate or major rework |

### Decision Matrix
```yaml
decision_matrix:
  high_adoption_high_outcome:
    verdict: "Success"
    action: "Expand to more users/segments"
  
  high_adoption_low_outcome:
    verdict: "Underperforming"
    action: "Feature is used but not delivering value—redesign"
  
  low_adoption_high_outcome:
    verdict: "Partial success"
    action: "Value proven, address adoption blockers"
  
  low_adoption_low_outcome:
    verdict: "Failure"
    action: "Reconsider problem hypothesis, likely deprecate"
```

## Analysis Timeframes

| Feature Type | Measurement Period | Reason |
|--------------|-------------------|--------|
| Quick action | 1-2 weeks | Fast feedback cycle |
| Workflow change | 4-6 weeks | Habit formation time |
| Strategic feature | 8-12 weeks | Full adoption curve |
| Platform change | 3-6 months | Long adoption tail |

## Segmentation Analysis

Break down results by segment:

```yaml
segment_comparison:
  - segment: "Enterprise customers"
    sample_size: 450
    adoption_rate: "35%"
    outcome_improvement: "+52%"
    insight: "High value for this segment"
  
  - segment: "SMB customers"
    sample_size: 3200
    adoption_rate: "8%"
    outcome_improvement: "+15%"
    insight: "Low adoption—discoverability issue or not relevant?"
  
  recommendation: |
    Double down on enterprise. Investigate SMB 
    adoption barriers before further investment.
```

## Learning Documentation

### Capture for Future Features
```yaml
learnings:
  what_worked:
    - "In-app announcement drove 3x more awareness than email"
    - "Tooltip tutorial increased trial rate by 25%"
  
  what_didnt_work:
    - "Default OFF setting led to low discovery"
    - "Help documentation wasn't linked from UI"
  
  unexpected:
    - "Power users found workaround that was faster"
    - "Adjacent workflow improved unexpectedly"
  
  apply_to_future:
    - "Always default new features ON for subset first"
    - "Link contextual help from feature UI"
    - "Watch for workaround patterns as improvement signals"
```

## Analysis Checklist

- [ ] Sufficient time elapsed since launch
- [ ] Adoption funnel tracked end-to-end
- [ ] Primary outcome metric measured
- [ ] Statistical significance assessed
- [ ] Segment differences analyzed
- [ ] Qualitative feedback collected
- [ ] Unintended impacts noted
- [ ] Verdict is evidence-based
- [ ] Next steps are actionable
- [ ] Learnings documented

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
