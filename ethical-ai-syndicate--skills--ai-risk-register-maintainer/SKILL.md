---
name: ai-risk-register-maintainer
description: Use when tracking AI-specific risks across the portfolio. Use continuously. Produces risk registry, risk scoring, mitigation tracking, and reporting for governance.
metadata:
  author: ethical-ai-syndicate
---

# AI Risk Register Maintainer

## Overview

Track AI-specific risks across the organization's AI portfolio. Maintain a living registry of risks, assess their likelihood and impact, track mitigations, and report to governance.

**Core principle:** AI introduces novel risks that traditional IT risk frameworks miss. Maintain dedicated visibility into AI-specific concerns.

## When to Use

- Setting up AI governance
- Onboarding new AI systems
- Quarterly risk reviews
- Incident post-mortems
- Regulatory reporting

## Output Format

```yaml
ai_risk_register:
  version: "[Version]"
  last_updated: "[YYYY-MM-DD]"
  owner: "[Risk owner]"
  
  risk_categories:
    - category: "[Category name]"
      description: "[What risks this covers]"
  
  risks:
    - id: "[RISK-001]"
      title: "[Risk title]"
      category: "[Category]"
      
      description:
        risk_statement: "[If X, then Y, resulting in Z]"
        affected_systems: ["[System 1]", "[System 2]"]
        affected_stakeholders: ["[Stakeholder]"]
      
      assessment:
        likelihood: "[1-5]"
        impact: "[1-5]"
        risk_score: "[Calculated]"
        risk_level: "[Critical | High | Medium | Low]"
        velocity: "[How fast could it materialize]"
        
      current_state:
        status: "[Open | Mitigating | Accepted | Closed]"
        trend: "[Increasing | Stable | Decreasing]"
        last_reviewed: "[YYYY-MM-DD]"
        
      controls:
        existing:
          - control: "[Current control]"
            effectiveness: "[High | Medium | Low]"
        
        planned:
          - control: "[Planned mitigation]"
            owner: "[Who]"
            due_date: "[When]"
            status: "[Not started | In progress | Complete]"
      
      residual_risk:
        after_controls: "[1-5]"
        acceptable: [true | false]
        acceptance_authority: "[If accepted, by whom]"
      
      monitoring:
        indicators: ["[KRI/early warning sign]"]
        frequency: "[How often checked]"
      
      related_incidents: ["[INC-XXX]"]
  
  summary:
    total_risks: "[N]"
    by_level:
      critical: "[N]"
      high: "[N]"
      medium: "[N]"
      low: "[N]"
    
    trend: "[Overall risk posture trend]"
    top_risks: ["[RISK-001]", "[RISK-002]", "[RISK-003]"]
    
  governance:
    review_schedule: "[Frequency]"
    escalation_path: "[Who to escalate to]"
    reporting_to: ["[Committee/Board]"]
```

## AI-Specific Risk Categories

### Model Risks
| Risk | Description | Example |
|------|-------------|---------|
| **Accuracy degradation** | Model performance declines over time | Drift causes 20% accuracy drop |
| **Hallucination** | LLM generates false information | Chatbot invents policy |
| **Adversarial attack** | Malicious inputs cause failure | Prompt injection |
| **Overfitting** | Model fails on new data | Works in test, fails in prod |

### Data Risks
| Risk | Description | Example |
|------|-------------|---------|
| **Training data bias** | Biased data leads to biased outcomes | Hiring tool discriminates |
| **Data poisoning** | Malicious data corrupts model | Attacker injects bad labels |
| **Data leakage** | PII exposed through model | Model memorizes SSNs |
| **Data quality** | Poor data quality degrades performance | Corrupted input causes errors |

### Operational Risks
| Risk | Description | Example |
|------|-------------|---------|
| **Model unavailability** | AI service is down | Inference API outage |
| **Scaling failure** | Can't handle load | Black Friday traffic spikes |
| **Integration failure** | AI breaks downstream systems | Bad output corrupts database |
| **Vendor dependency** | Over-reliance on AI provider | OpenAI API changes break app |

### Ethical & Compliance Risks
| Risk | Description | Example |
|------|-------------|---------|
| **Unfair outcomes** | Disparate impact on groups | Loan denial rate disparity |
| **Lack of explainability** | Can't explain decisions | Regulatory requires explanation |
| **Consent violation** | Using data without consent | Training on private data |
| **Regulatory non-compliance** | Violating AI regulations | EU AI Act violation |

### Strategic Risks
| Risk | Description | Example |
|------|-------------|---------|
| **Misalignment with business** | AI doesn't serve business goals | Optimizing wrong metric |
| **Competitive exposure** | Competitors gain advantage | Slow AI adoption |
| **Reputation damage** | AI incident harms brand | Viral AI failure screenshot |
| **Skills shortage** | Can't hire/retain AI talent | Projects stall |

## Risk Scoring

### Likelihood Scale
| Score | Description | Frequency |
|-------|-------------|-----------|
| 5 | Almost certain | >90% in next year |
| 4 | Likely | 60-90% |
| 3 | Possible | 30-60% |
| 2 | Unlikely | 10-30% |
| 1 | Rare | <10% |

### Impact Scale
| Score | Description | Example |
|-------|-------------|---------|
| 5 | Catastrophic | Regulatory action, major financial loss, safety incident |
| 4 | Major | Significant financial loss, extended outage, legal action |
| 3 | Moderate | Material financial impact, degraded service, reputation hit |
| 2 | Minor | Limited impact, quickly recoverable |
| 1 | Negligible | Minimal impact, no lasting effect |

### Risk Matrix
```
          │ Impact
Likelihood│  1    2    3    4    5
──────────┼──────────────────────────
    5     │  M    M    H    C    C
    4     │  L    M    H    H    C
    3     │  L    M    M    H    H
    2     │  L    L    M    M    H
    1     │  L    L    L    M    M

L=Low, M=Medium, H=High, C=Critical
```

## Risk Monitoring

### Key Risk Indicators (KRIs)
```yaml
kri_examples:
  accuracy_degradation:
    indicator: "Model accuracy vs baseline"
    threshold: ">5% drop triggers review"
    frequency: "Daily"
  
  bias_drift:
    indicator: "Fairness metrics by group"
    threshold: "DI ratio <0.8 triggers alert"
    frequency: "Weekly"
  
  incident_rate:
    indicator: "AI-related incidents per month"
    threshold: ">3 per month triggers review"
    frequency: "Monthly"
```

## Governance Reporting

### Executive Summary Template
```markdown
## AI Risk Summary - [Quarter]

**Overall Risk Posture:** [Green/Amber/Red]

### Top Risks
1. [Risk] - [Level] - [Trend]
2. [Risk] - [Level] - [Trend]
3. [Risk] - [Level] - [Trend]

### Key Changes
- [New risk added]
- [Risk level changed]
- [Mitigation completed]

### Actions Required
- [Decision needed from leadership]
```

## Register Maintenance

### Review Cadence
| Activity | Frequency | Participants |
|----------|-----------|--------------|
| Risk identification | Ongoing | All teams |
| Risk assessment review | Monthly | Risk owners |
| Full register review | Quarterly | Risk committee |
| Board reporting | Quarterly/Annual | Leadership |

### Triggers for Update
- New AI system deployed
- Significant model change
- Incident occurred
- Regulatory change
- External threat identified

## Checklist

- [ ] All AI systems covered
- [ ] Risk categories comprehensive
- [ ] Scoring consistent
- [ ] Controls documented
- [ ] Owners assigned
- [ ] Monitoring in place
- [ ] Review scheduled
- [ ] Governance reporting current

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
