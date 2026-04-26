---
name: explainability-documenter
description: Use when documenting AI explainability for stakeholders. Use after model development. Produces human-readable explanations, documentation, and disclosure templates.
metadata:
  author: ethical-ai-syndicate
---

# Explainability Documenter

## Overview

Document AI system explanations for various stakeholders. Create human-readable descriptions of how AI makes decisions, appropriate for regulatory, user, and internal audiences.

**Core principle:** Explainability is audience-specific. Technical accuracy matters less than appropriate communication for each stakeholder.

## When to Use

- Regulatory compliance requirements
- User-facing transparency
- Internal governance documentation
- Audit preparation
- Stakeholder communication

## Output Format

```yaml
explainability_documentation:
  system: "[System name]"
  date: "[YYYY-MM-DD]"
  
  system_overview:
    purpose: "[What the system does]"
    decision_type: "[Recommendation | Prediction | Classification]"
    role_in_process: "[How humans use the output]"
  
  audiences:
    - audience: "[Stakeholder group]"
      needs: "[What they need to understand]"
      format: "[How to communicate]"
  
  technical_explanation:
    model_type: "[Type of model]"
    key_features:
      - feature: "[Feature name]"
        importance: "[Relative importance]"
        description: "[What it means]"
    
    methodology: "[How decisions are made]"
    limitations: ["[Known limitations]"]
  
  user_explanation:
    summary: "[Plain language explanation]"
    factors_considered: ["[Factor 1]", "[Factor 2]"]
    factors_not_considered: ["[Factor 1]"]
    how_to_interpret: "[Guidance for users]"
  
  individual_explanation_template:
    structure: "[Format for per-decision explanations]"
    example: "[Sample explanation]"
  
  regulatory_documentation:
    methodology_description: "[Technical methodology]"
    validation_summary: "[Testing performed]"
    monitoring: "[Ongoing oversight]"
  
  appeals_process:
    how_to_request: "[Process]"
    what_happens: "[What user can expect]"
```

## Audience-Specific Explanations

### End Users
```yaml
user_explanation:
  principles:
    - "Plain language, no jargon"
    - "Focus on factors, not algorithms"
    - "Actionable information"
  
  template: |
    This recommendation is based on:
    • [Factor 1]: [How it influenced the result]
    • [Factor 2]: [How it influenced the result]
    
    This does NOT consider:
    • [Factor not used]
    
    If you believe this is incorrect, [appeal process].
```

### Regulators
```yaml
regulatory_explanation:
  principles:
    - "Technical accuracy"
    - "Methodology transparency"
    - "Validation evidence"
  
  sections:
    - "Model description and architecture"
    - "Training data and methodology"
    - "Feature engineering"
    - "Validation and testing results"
    - "Fairness assessment"
    - "Monitoring and controls"
```

### Business Stakeholders
```yaml
business_explanation:
  principles:
    - "Business context"
    - "Value and limitations"
    - "Decision rights"
  
  template: |
    The AI system [does what] to help [outcome].
    It considers [key factors] and produces [output type].
    Human judgment is required for [aspects].
    Key limitations include [limitations].
```

## Explanation Methods

### Feature Importance
```yaml
feature_importance:
  global: "Which features matter most overall"
  local: "Which features mattered for this decision"
  
  presentation:
    - "Rank order of features"
    - "Direction of influence"
    - "Plain language description"
```

### Example-Based
```yaml
example_based:
  counterfactuals: |
    "If [factor] had been [different value], 
    the recommendation would have been [different outcome]"
  
  similar_cases: |
    "This is similar to [X] previous cases 
    that had [outcome]"
```

### Rule Summaries
```yaml
rule_summary:
  when_possible: "Summarize key decision logic as rules"
  
  example: |
    Key factors in this decision:
    • High score IF [condition A] AND [condition B]
    • Lower score IF [condition C]
```

## Documentation Structure

### Model Card Template
```yaml
model_card:
  model_details:
    - "Name and version"
    - "Type and architecture"
    - "Developers and date"
  
  intended_use:
    - "Primary use case"
    - "Users"
    - "Out of scope uses"
  
  factors:
    - "What influences outputs"
    - "What doesn't"
  
  metrics:
    - "How performance is measured"
    - "Performance results"
  
  limitations:
    - "Known limitations"
    - "Recommendations for use"
  
  ethical_considerations:
    - "Fairness assessment"
    - "Potential misuse"
```

## Transparency Disclosure

### For Automated Decisions
```yaml
disclosure_template:
  when_to_show: "Before or at point of decision"
  
  content:
    - "This decision used AI assistance"
    - "Key factors considered"
    - "Human oversight in place"
    - "How to appeal or get human review"
  
  example: |
    This recommendation was generated with AI assistance.
    Key factors: [factors]
    A human reviewer [will review / can review upon request].
    To request human review: [process]
```

## Checklist

- [ ] All stakeholder audiences identified
- [ ] Technical explanation documented
- [ ] User-friendly explanation created
- [ ] Regulatory requirements met
- [ ] Individual explanation template ready
- [ ] Limitations clearly stated
- [ ] Appeal process documented

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
