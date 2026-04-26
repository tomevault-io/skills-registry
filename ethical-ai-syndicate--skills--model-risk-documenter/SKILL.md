---
name: model-risk-documenter
description: Use when documenting AI/ML models for Model Risk Management review in financial services. Use before MRM submission. Generates SR 11-7 and OCC 2011-12 compliant documentation packages.
metadata:
  author: ethical-ai-syndicate
---

# Model Risk Documenter

## Overview

Generate comprehensive model documentation that satisfies OCC 2011-12 and Federal Reserve SR 11-7 requirements. The goal is documentation that enables MRM review and approval.

**Core principle:** Model documentation isn't about explaining how it works to engineers. It's about enabling risk-informed decisions by people who may not understand the technical details.

## MRM Tier Classification

Classify the model FIRST - tier drives documentation requirements:

| Tier | Criteria | Documentation Depth |
|------|----------|---------------------|
| Tier 1 - High | Material financial impact, regulatory reporting, customer decisions | Full documentation, independent validation, ongoing monitoring, annual revalidation |
| Tier 2 - Material | Operational impact, internal decisions, moderate complexity | Complete documentation, periodic validation, performance monitoring |
| Tier 3 - Low | Low impact, simple models, minimal risk | Basic documentation, testing evidence |

**Tier Classification Factors:**

```yaml
tier_assessment:
  financial_impact:
    question: "What's the $ impact if model is wrong?"
    tier_1: ">$10M potential or regulatory capital"
    tier_2: "$1M-$10M potential"
    tier_3: "<$1M potential"

  decision_automation:
    question: "Does model make decisions or support them?"
    tier_1: "Automated decisions affecting customers/reporting"
    tier_2: "Decision support with human review"
    tier_3: "Analytics/reporting only"

  complexity:
    question: "How complex is the methodology?"
    tier_1: "Complex ML, many features, opaque"
    tier_2: "Moderate complexity, interpretable"
    tier_3: "Simple rules, transparent logic"

  reversibility:
    question: "Can errors be detected and corrected?"
    tier_1: "Hard to detect, irreversible impact"
    tier_2: "Detectable, correctable with effort"
    tier_3: "Easily detected and reversed"
```

## Output Format

```yaml
model_documentation:
  document_type: "Model Documentation - OCC 2011-12 / SR 11-7 Compliant"
  model_name: "[Name]"
  model_id: "[Inventory ID]"
  version: "[Version]"
  effective_date: "[Date]"
  document_owner: "[Model Owner - Name/Role]"
  business_owner: "[Business Owner - Name/Role]"

executive_summary:
  purpose: |
    [What business problem does this model solve?]
    [Why is a model needed vs. rules or manual process?]

  business_use: |
    [How is the model output used?]
    [Who uses it and for what decisions?]
    [What happens if model is unavailable?]

  materiality: |
    [Quantified business impact]
    [$ values where possible]
    [Volume of decisions affected]

  mrm_tier: "[Tier 1/2/3]"
  tier_rationale: |
    [Explicit reasoning for tier classification]
    [Reference tier criteria above]

model_purpose_and_use:
  intended_use:
    - "[Primary use case]"
    - "[Secondary use cases]"

  decision_context: |
    [Is output used directly or as input to human decision?]
    [What other factors influence the decision?]

  users:
    - role: "[User role]"
      usage: "[How they use model output]"

  prohibited_uses:
    - "[Uses the model should NOT be applied to]"

methodology:
  model_type: "[Algorithm family]"

  theoretical_basis: |
    [Why does this approach make sense for this problem?]
    [What's the conceptual foundation?]
    [Literature or industry support]

  alternative_approaches_considered:
    - approach: "[Alternative 1]"
      reason_rejected: "[Why not chosen]"
    - approach: "[Alternative 2]"
      reason_rejected: "[Why not chosen]"

  feature_categories:
    category_name:
      - feature: "[Feature name]"
        description: "[What it measures]"
        rationale: "[Why it's predictive]"
        source: "[Data source]"

  model_specification:
    algorithm: "[Specific algorithm]"
    hyperparameters:
      param_name: value
    regularization: "[Techniques used]"
    training_approach: "[How trained]"

data_requirements:
  training_data:
    source: "[Data sources]"
    period: "[Time range]"
    volume: "[Record count]"
    target_definition: "[Exact definition of target variable]"
    target_rate: "[Base rate in training data]"

  data_quality:
    completeness: "[% complete]"
    known_issues:
      - issue: "[Issue description]"
        mitigation: "[How addressed]"

  feature_engineering:
    derived_features:
      - "[Engineered feature descriptions]"
    transformations:
      - "[Data transformations applied]"

  ongoing_data_requirements:
    - "[What data needed for inference]"
    - "[Refresh requirements]"

performance:
  development_testing:
    methodology: "[Train/test split approach]"
    holdout_period: "[Time period held out]"

    metrics:
      - metric: "[Metric name]"
        value: "[Value]"
        interpretation: "[What this means in business terms]"
        threshold: "[Acceptable range]"

  business_context:
    baseline_comparison: |
      [How does model compare to current process?]
      [Quantified improvement]

    operational_capacity: |
      [Can operations handle model volume?]
      [Resource implications]

  stability_testing:
    - test: "[Stability test name]"
      result: "[Result]"
      interpretation: "[What it means]"

limitations_and_assumptions:
  key_assumptions:
    - assumption: "[Assumption]"
      risk: "[What happens if assumption violated]"
      mitigation: "[How monitored/addressed]"

  known_limitations:
    - limitation: "[Limitation]"
      impact: "[Business impact]"
      mitigation: "[Workaround]"

  conditions_of_poor_performance:
    - "[Scenario where model underperforms]"

validation:
  initial_validation:
    scope:
      - "[Validation component]"
    status: "[Pending/Complete]"
    validator: "[Independent validator]"

  ongoing_validation:
    frequency: "[How often]"
    trigger_events:
      - "[What triggers ad-hoc validation]"

ongoing_monitoring:
  key_performance_indicators:
    - kpi: "[Metric]"
      threshold: "[Acceptable range]"
      frequency: "[How often measured]"
      escalation: "[What happens if breached]"

  monitoring_reports:
    - report: "[Report name]"
      frequency: "[How often]"
      audience: "[Who receives]"

  model_inventory_updates:
    frequency: "[How often updated]"
    owner: "[Who updates]"

change_management:
  changes_requiring_revalidation:
    - "[Change type that triggers revalidation]"

  changes_requiring_notification:
    - "[Change type requiring MRM notification]"

  documentation_updates:
    - "[Documentation refresh requirements]"

approval_and_attestation:
  model_owner_attestation: |
    I attest that this documentation accurately represents the model.

  required_approvals:
    - approver: "[Role]"
      status: "[Status]"

appendices:
  appendix_a: "[Feature dictionary reference]"
  appendix_b: "[Validation report reference]"
  appendix_c: "[Model changelog]"
```

## OCC 2011-12 / SR 11-7 Requirements Mapping

| Requirement | Section | Key Content |
|-------------|---------|-------------|
| Model purpose and use | Purpose, Business Use | Clear statement of what model does and how used |
| Conceptual soundness | Methodology | Theoretical basis, why approach makes sense |
| Data quality | Data Requirements | Source quality, known issues, mitigations |
| Developmental evidence | Performance | Testing methodology and results |
| Limitations | Limitations | What can go wrong, when model fails |
| Ongoing monitoring | Monitoring | KPIs, thresholds, reporting |
| Validation | Validation | Independent review scope and schedule |
| Change management | Change Management | What triggers review |

## Conceptual Soundness Section

**This is often the weakest part of model documentation.** MRM wants to know WHY this approach should work, not just THAT it works empirically.

**Address:**
- Theoretical foundation (economics, statistics, domain expertise)
- Literature support if available
- Why features should be predictive (not just that they are)
- Why this algorithm vs. alternatives
- Known limitations of the approach

**Example - Good:**
```yaml
theoretical_basis: |
  Settlement failure is driven by operational friction, counterparty stress,
  and market conditions. Gradient boosting captures non-linear interactions
  between these factors - for example, a counterparty with elevated fail rate
  combined with illiquid security and short settlement window creates
  compounding risk that linear models miss.

  Industry research (DTCC Settlement Studies, 2023) confirms that
  counterparty-level factors explain ~60% of fail variance.
```

**Example - Bad:**
```yaml
theoretical_basis: |
  We used XGBoost because it had the highest AUC in testing.
```

## Limitation Documentation

**Regulators focus heavily on limitations.** Under-documenting limitations is a red flag.

**Categories to address:**

| Category | Example | Documentation Approach |
|----------|---------|----------------------|
| Data limitations | New entities have no history | State limitation, impact, mitigation |
| Model limitations | Assumes stable relationships | State assumption, monitoring approach |
| Scope limitations | Only trained on corporate bonds | Explicitly state in/out of scope |
| Operational limitations | Requires data within SLA | State dependency, fallback |

## Performance Documentation

**Translate technical metrics to business impact:**

| Technical | Business Translation |
|-----------|---------------------|
| AUC 0.85 | "Model ranks risk well; top decile captures 6x baseline" |
| Precision 78% | "78% of flagged items are true positives; 22% false alarm rate acceptable for triage" |
| False negative 12% | "12% of failures not caught; downstream reconciliation provides safety net" |

## Ongoing Monitoring Framework

Every model needs defined monitoring:

```yaml
monitoring_framework:
  performance_monitoring:
    - metric: "[Primary performance metric]"
      baseline: "[Expected value]"
      warning: "[Threshold for investigation]"
      critical: "[Threshold for action]"
      frequency: "Monthly"

  stability_monitoring:
    - metric: "Population Stability Index"
      threshold: "<0.10 stable, 0.10-0.25 investigate, >0.25 action"
      frequency: "Monthly"

  data_quality_monitoring:
    - check: "Input completeness"
      threshold: ">99%"
      frequency: "Daily"

  escalation_path:
    warning: "Model owner investigation"
    critical: "MRM notification within 5 business days"
    breach: "Model suspension pending review"
```

## Common Mistakes

| Mistake | Why It's Wrong | Do This Instead |
|---------|----------------|-----------------|
| No tier classification | Drives all requirements | Classify first with rationale |
| "Model is accurate" | Not business context | Translate to business impact |
| Minimal limitations | Raises MRM concern | Document thoroughly |
| No conceptual soundness | Core SR 11-7 requirement | Explain WHY it works |
| Validation = testing | Independent validation required | Plan for independent review |
| Static documentation | Models change | Define update triggers |
| Technical audience | MRM may not be technical | Plain language throughout |

## Red Flags in Your Documentation

If your documentation has these, it's not ready:

- No MRM tier with rationale
- Technical metrics without business context
- Limitations section is short
- No "why" in methodology - just "what"
- Monitoring thresholds undefined
- Change management not addressed
- No attestation section

## Financial Services Context

Model risk documentation for financial services requires:

### Regulatory Framework Awareness
- OCC 2011-12 structures expectations
- SR 11-7 provides additional Fed guidance
- Tier classification drives depth

### Independent Validation Expectation
- Tier 1: Independent validation required
- Tier 2: Periodic validation
- Documentation must enable validation

### Examination Readiness
- Examiners will request model documentation
- "Show me the limitations" is standard question
- Monitoring evidence must be producible

### Model Inventory Integration
- Document must align with model inventory
- Consistent IDs, ownership, classification
- Regular inventory updates required

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
