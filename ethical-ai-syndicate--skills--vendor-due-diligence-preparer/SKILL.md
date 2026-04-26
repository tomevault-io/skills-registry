---
name: vendor-due-diligence-preparer
description: Use when evaluating third-party AI/ML vendors for financial services. Use before contract signing or renewal. Produces comprehensive due diligence with AI-specific assessment, gap analysis, and approval recommendations.
metadata:
  author: ethical-ai-syndicate
---

# Vendor Due Diligence Preparer

## Overview

Prepare comprehensive due diligence packages for third-party AI/ML vendors that address regulatory expectations, information security, financial stability, and AI-specific risks.

**Core principle:** AI vendors introduce unique risks beyond traditional technology vendors. Model governance, training data practices, and explainability require specific assessment.

## Vendor Criticality Classification

Classify vendor FIRST - criticality drives due diligence depth:

| Tier | Criteria | Due Diligence Depth |
|------|----------|---------------------|
| CRITICAL | Real-time integration, no manual fallback, customer-facing | Enhanced due diligence, onsite review, financial deep-dive |
| HIGH | Important function, degraded fallback possible | Full due diligence questionnaire, financial review |
| MEDIUM | Supplementary function, manual backup exists | Standard questionnaire, basic financial check |
| LOW | Non-essential, easily replaceable | Abbreviated review |

## Output Format

```yaml
vendor_due_diligence:
  vendor: "[Vendor Name]"
  product: "[Product/Service]"
  assessment_date: "[Date]"
  assessor: "[Role/Team]"
  use_case: "[How we'll use it]"

vendor_classification:
  criticality_tier: "[CRITICAL | HIGH | MEDIUM | LOW]"
  tier_rationale: |
    [Why this tier]
  risk_category: ["[Risk types: Operational, Data, Model, Concentration]"]
  due_diligence_depth: "[Enhanced | Full | Standard | Abbreviated]"

information_security_assessment:
  certifications:
    - certification: "[Cert name]"
      scope: "[What's covered]"
      last_audit: "[Date]"
      status: "[VERIFIED | REQUIRES VERIFICATION]"

  data_security:
    encryption_in_transit: "[Standard]"
    encryption_at_rest: "[Standard]"
    access_controls: "[Description]"
    status: "[Status]"

  security_questions_required:
    - "[Questions to ask vendor]"

business_continuity:
  infrastructure:
    hosting: "[Where hosted]"
    redundancy: "[HA approach]"
    disaster_recovery: "[DR approach or UNKNOWN]"

  sla_analysis:
    committed: "[SLA %]"
    assessment: "[Is it sufficient?]"
    recommendation: "[Negotiate if needed]"

  continuity_questions_required:
    - "[Questions to ask]"

data_handling:
  data_we_send:
    - data_element: "[What we send]"
      sensitivity: "[Classification]"
      concern: "[Any concerns]"

  data_they_return:
    - data_element: "[What we receive]"
      retention_by_vendor: "[How long they keep it]"

  data_concerns:
    - concern: "[Specific concern]"
      risk: "[Risk description]"
      mitigation: "[How to address]"

  data_questions_required:
    - "[Questions about data handling]"

ai_ml_governance:
  model_information:
    model_type: "[What kind of AI/ML]"
    training_data: "[What they say about training]"
    retraining_frequency: "[How often updated]"
    status: "[SUFFICIENT | INSUFFICIENT DETAIL]"

  ai_specific_questions_required:
    - "[AI governance questions]"

  ai_risk_assessment:
    - risk: "[AI-specific risk]"
      likelihood: "[HIGH | MEDIUM | LOW | UNKNOWN]"
      impact: "[Business impact]"
      mitigation: "[How to address]"

financial_stability:
  provided_information:
    funding: "[What vendor provided]"
    # ... other provided info

  concerns:
    - concern: "[Financial concern]"
      question: "[Question to ask]"

  financial_questions_required:
    - "[Financial stability questions]"

  assessment: "[Summary assessment]"

regulatory_compliance:
  vendor_regulatory_status:
    - question: "[Regulatory question]"
      status: "[KNOWN | UNKNOWN]"

  our_regulatory_considerations:
    - regulation: "[Applicable regulation]"
      requirement: "[What it requires]"
      status: "[How addressed]"

contract_analysis:
  concerning_terms:
    - term: "[Contract term]"
      concern: "[Why concerning]"
      recommendation: "[What to negotiate]"

  missing_terms:
    - "[Terms that should be added]"

  contract_recommendations:
    - "[Specific negotiation points]"

concentration_risk:
  current_portfolio:
    - category: "[Vendor category]"
      primary_vendor: "[Existing]"
      this_vendor_adds: "[Impact on concentration]"

  assessment: "[Concentration risk summary]"

gap_summary:
  information_gaps:
    - gap: "[Missing information]"
      priority: "[HIGH | MEDIUM | LOW]"
      action: "[How to fill gap]"

  contract_gaps:
    - gap: "[Missing contract protection]"
      priority: "[Priority]"
      action: "[Negotiation action]"

risk_summary:
  identified_risks:
    - risk: "[Risk description]"
      rating: "[HIGH | MEDIUM | LOW]"
      rationale: "[Why this rating]"
      mitigation: "[How to mitigate]"

  overall_risk_rating: "[Overall rating]"

recommendation:
  decision: "[APPROVE | CONDITIONAL APPROVAL | DEFER | REJECT]"
  conditions:
    - condition: "[What must happen]"
      owner: "[Who owns]"
      deadline: "[When]"

  ongoing_monitoring:
    - "[Monitoring requirement]"
```

## AI/ML-Specific Due Diligence Questions

These questions differentiate AI vendor assessment from standard technology vendors:

### Model Governance

| Question | Why It Matters |
|----------|----------------|
| How is the model validated before deployment? | Ensures quality control |
| What is the model change management process? | Protects against unexpected changes |
| How do you detect and address model drift? | Ensures ongoing accuracy |
| What testing is done before model updates? | Prevents regression |

### Training Data

| Question | Why It Matters |
|----------|----------------|
| What data is the model trained on? | Understand model foundation |
| How is training data quality assured? | Garbage in = garbage out |
| Is our data used to train/improve the model? | Privacy and competitive concerns |
| Can we opt out of model training? | Control over our data |

### Explainability

| Question | Why It Matters |
|----------|----------------|
| Can you explain individual predictions? | Regulatory requirement for some uses |
| What documentation exists for the model? | Supports our MRM requirements |
| How do you handle edge cases? | Understand limitations |

### Bias and Fairness

| Question | Why It Matters |
|----------|----------------|
| What bias testing is performed? | Fair lending, discrimination concerns |
| How is fairness monitored over time? | Ongoing compliance |
| What is the process if bias is detected? | Remediation capability |

## Financial Stability Assessment

AI vendors are often startups. Assess carefully:

### Key Questions

| Topic | Question | Red Flag |
|-------|----------|----------|
| Runway | What is current cash runway? | <12 months |
| Revenue | What is revenue concentration? | >50% from top 3 clients |
| Path | Path to profitability or next round? | Unclear |
| Key person | Founder/key technical dependency? | Single technical founder |

### Assessment Framework

```yaml
financial_assessment:
  funding_stage: "[Seed | Series A | B | C | Growth | Public]"
  implied_runway: "[Based on typical burn]"
  revenue_concentration_risk: "[HIGH | MEDIUM | LOW]"
  key_person_risk: "[HIGH | MEDIUM | LOW]"
  acquisition_likelihood: "[HIGH | MEDIUM | LOW]"
  overall_stability: "[Stable | Moderate risk | Elevated risk | High risk]"
```

## Contract Terms Checklist

For AI vendors, ensure:

### Must-Have Terms

- [ ] Right to audit (or independent audit report)
- [ ] Data breach notification (24-48 hours)
- [ ] Subcontractor notification and approval
- [ ] Data deletion upon termination
- [ ] SLA with performance credits
- [ ] Termination for cause with reasonable notice
- [ ] Limitation of liability appropriate to risk
- [ ] Insurance requirements (cyber, E&O)

### AI-Specific Terms

- [ ] Model change notification
- [ ] Data usage restrictions (opt-out of training)
- [ ] Performance guarantees (accuracy, latency)
- [ ] Explainability commitments
- [ ] Indemnification for model errors

### Red Flag Terms

- [ ] Unlimited data usage rights
- [ ] Unilateral contract modification
- [ ] No termination for cause
- [ ] Liability caps below risk exposure
- [ ] No audit rights

## Regulatory Framework

Reference these in your assessment:

| Guidance | Focus | Key Requirements |
|----------|-------|------------------|
| OCC 2013-29 | Third-party risk | Risk management commensurate with risk |
| FFIEC IT Handbook | Technology providers | Due diligence, ongoing monitoring |
| SR 11-7 | Model risk | Third-party model governance |
| State regulations | Various | May have specific vendor requirements |

## Concentration Risk Analysis

Always evaluate in portfolio context:

```yaml
concentration_analysis:
  existing_vendors_in_category:
    - vendor: "[Existing vendor]"
      dependency: "[What depends on it]"

  this_vendor_impact:
    creates_new_concentration: true|false
    increases_existing_concentration: true|false
    provides_backup_option: true|false

  recommendation: "[Accept | Require backup identification | Reject]"
```

## Common Mistakes

| Mistake | Why It's Wrong | Do This Instead |
|---------|----------------|-----------------|
| Standard IT checklist only | Misses AI-specific risks | Add AI governance section |
| "Series B funded" = stable | Runway varies widely | Ask specific financial questions |
| Focus on tech only | Contract terms create risk | Review contract carefully |
| Evaluate in isolation | Concentration risk is real | Assess portfolio impact |
| Skip data usage analysis | Competitive and privacy risk | Understand exactly how data used |
| Accept SOC 2 at face value | Scope may be limited | Review actual report |

## Red Flags in Your Assessment

If your assessment has these, it's incomplete:

- No criticality classification with rationale
- Missing AI-specific governance questions
- Financial stability accepted without verification
- Data handling not analyzed in detail
- Contract terms not reviewed
- No concentration risk consideration
- Recommendation without conditions

## Financial Services Context

Vendor due diligence for AI providers in financial services requires:

### Regulatory Awareness
- OCC 2013-29 frames expectations
- Third-party risk is examination topic
- Model risk guidance applies to vendor models

### AI-Specific Rigor
- Training data practices matter
- Model governance is our responsibility too
- Explainability may be regulatory requirement

### Financial Scrutiny
- AI startups common; stability varies
- Assess runway realistically
- Plan for vendor failure

### Contract Discipline
- Standard SaaS terms often insufficient
- Negotiate AI-specific protections
- Ensure exit rights

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
