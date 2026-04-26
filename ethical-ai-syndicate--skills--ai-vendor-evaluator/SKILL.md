---
name: ai-vendor-evaluator
description: Use when comparing AI platforms, tools, or services. Use before procurement. Produces structured vendor comparison, scoring, and recommendation.
metadata:
  author: ethical-ai-syndicate
---

# AI Vendor Evaluator

## Overview

Conduct structured evaluation of AI vendors, platforms, and services. Compare options objectively and make defensible procurement decisions.

**Core principle:** AI vendor selection is a strategic decision. Evaluate beyond features—consider support, roadmap, security, and total cost.

## When to Use

- Selecting LLM provider
- Choosing ML platform
- Evaluating AI SaaS tools
- Renewing vendor contracts
- Build vs buy decisions

## Output Format

```yaml
vendor_evaluation:
  project: "[What we're selecting for]"
  evaluation_date: "[YYYY-MM-DD]"
  evaluators: ["[Name]"]
  
  requirements:
    must_have:
      - requirement: "[Requirement]"
        rationale: "[Why essential]"
    
    should_have:
      - requirement: "[Requirement]"
        weight: "[1-5]"
    
    nice_to_have:
      - requirement: "[Requirement]"
  
  vendors:
    - vendor: "[Vendor name]"
      product: "[Product name]"
      
      overview:
        description: "[What they offer]"
        pricing_model: "[How they charge]"
        contract_terms: "[Key terms]"
      
      scores:
        - category: "[Category]"
          criteria:
            - criterion: "[Criterion]"
              score: "[1-5]"
              evidence: "[How verified]"
              notes: "[Details]"
          category_score: "[Weighted average]"
      
      pros: ["[Advantage]"]
      cons: ["[Disadvantage]"]
      risks: ["[Concern]"]
      
      total_score: "[Weighted total]"
      rank: "[Position vs others]"
  
  comparison_matrix:
    headers: ["[Criterion]", "[Vendor A]", "[Vendor B]"]
    rows:
      - ["[Criterion 1]", "[Score]", "[Score]"]
  
  total_cost_analysis:
    - vendor: "[Vendor]"
      year_1: "[$]"
      year_3: "[$]"
      assumptions: ["[Assumption]"]
  
  recommendation:
    selected: "[Vendor name]"
    rationale: "[Why chosen]"
    conditions: ["[Any conditions]"]
    risks_accepted: ["[Known risks]"]
    next_steps: ["[Actions to proceed]"]
```

## Evaluation Categories

### Technical Capability
| Criterion | What to Evaluate |
|-----------|------------------|
| Model performance | Accuracy, quality on your data |
| Scalability | Handle your volume |
| Latency | Response time requirements |
| Customization | Fine-tuning, configuration options |
| Integration | APIs, SDKs, connectors |

### Security & Compliance
| Criterion | What to Evaluate |
|-----------|------------------|
| Data handling | Where data goes, retention |
| Certifications | SOC2, ISO27001, HIPAA |
| Privacy | GDPR compliance, consent |
| Access control | Authentication, authorization |
| Audit capability | Logging, monitoring |

### Operational
| Criterion | What to Evaluate |
|-----------|------------------|
| Reliability | SLA, uptime history |
| Support | Response time, expertise |
| Documentation | Quality, completeness |
| Monitoring | Observability tools |
| Disaster recovery | Backup, failover |

### Strategic
| Criterion | What to Evaluate |
|-----------|------------------|
| Vendor stability | Financial health, market position |
| Roadmap | Future direction alignment |
| Lock-in risk | Portability, standards |
| Ecosystem | Partners, community |

### Commercial
| Criterion | What to Evaluate |
|-----------|------------------|
| Pricing clarity | Understandable pricing |
| Cost predictability | Usage-based risks |
| Contract flexibility | Terms, exit clauses |
| Total cost | Implementation, operation, exit |

## Scoring Methodology

### Score Scale
| Score | Meaning |
|-------|---------|
| 5 | Excellent - exceeds requirements |
| 4 | Good - meets requirements well |
| 3 | Adequate - meets minimum requirements |
| 2 | Weak - partially meets requirements |
| 1 | Poor - does not meet requirements |

### Weighted Scoring
```yaml
category_weights:
  technical: 30%
  security: 25%
  operational: 20%
  strategic: 15%
  commercial: 10%
  
total_score: "Sum of (category_score × weight)"
```

## Evaluation Process

### Phase 1: Requirements (Week 1)
```yaml
steps:
  - "Document must-have requirements"
  - "Prioritize should-have requirements"
  - "Define evaluation criteria"
  - "Set weights by category"
  - "Identify stakeholders for input"
```

### Phase 2: Research (Week 2)
```yaml
steps:
  - "Identify vendor long list"
  - "Gather public information"
  - "Issue RFI if needed"
  - "Create short list (3-5 vendors)"
```

### Phase 3: Evaluate (Weeks 3-4)
```yaml
steps:
  - "Request demos"
  - "Technical proof of concept"
  - "Security questionnaire"
  - "Reference checks"
  - "Pricing negotiation"
```

### Phase 4: Decide (Week 5)
```yaml
steps:
  - "Score all criteria"
  - "Calculate weighted totals"
  - "Document recommendation"
  - "Present to stakeholders"
  - "Obtain approval"
```

## Proof of Concept Design

```yaml
poc_design:
  duration: "[2-4 weeks typical]"
  
  success_criteria:
    - criterion: "[What must be demonstrated]"
      measurement: "[How to measure]"
      threshold: "[Acceptable value]"
  
  test_scenarios:
    - scenario: "[Description]"
      data: "[What data to use]"
      expected: "[Expected outcome]"
  
  evaluation_questions:
    - "Does it work with our data?"
    - "What's the integration effort?"
    - "How does support respond to issues?"
    - "What's the actual cost at our scale?"
```

## TCO Calculation

```yaml
total_cost_of_ownership:
  year_1:
    license_subscription: "[$]"
    implementation: "[$]"
    integration: "[$]"
    training: "[$]"
    internal_effort: "[$]"
    total: "[$]"
  
  year_3:
    recurring: "[$ × 3]"
    scaling_assumptions: "[Volume growth]"
    price_changes: "[Expected changes]"
    total: "[$]"
  
  hidden_costs:
    - "Egress fees"
    - "Overage charges"
    - "Support tiers"
    - "Add-on features"
```

## Red Flags

| Red Flag | Concern |
|----------|---------|
| Unclear pricing | Unpredictable costs |
| No customer references | Limited real-world validation |
| Vague roadmap | May not evolve with needs |
| Poor security responses | Risk exposure |
| High switching costs | Lock-in |
| New/unstable company | Continuity risk |

## Checklist

- [ ] Requirements documented
- [ ] Evaluation criteria defined
- [ ] Weights assigned
- [ ] Short list created
- [ ] Demos completed
- [ ] POC executed (if needed)
- [ ] Security review done
- [ ] References checked
- [ ] TCO calculated
- [ ] Scores documented
- [ ] Recommendation justified

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
