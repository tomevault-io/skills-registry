---
name: regulatory-impact-assessor
description: Use when assessing AI projects against regulatory requirements. Use before deployment. Produces compliance assessment, gap analysis, and remediation plan.
metadata:
  author: ethical-ai-syndicate
---

# Regulatory Impact Assessor

## Overview

Assess AI projects against applicable regulatory requirements. Identify compliance gaps, document risk levels, and plan remediation to ensure lawful and ethical AI deployment.

**Core principle:** Regulatory compliance is not optional. Proactive assessment prevents costly remediation and enforcement actions.

## When to Use

- Before AI project approval
- Before production deployment
- When regulations change
- Annual compliance review
- After significant model changes

## Output Format

```yaml
regulatory_assessment:
  project: "[Project name]"
  assessment_date: "[YYYY-MM-DD]"
  assessor: "[Name/Team]"
  
  system_profile:
    description: "[What the AI does]"
    decision_type: "[Support | Automation | Recommendation]"
    affected_parties: "[Who is impacted]"
    data_types: ["[Data type]"]
    deployment_regions: ["[Region]"]
  
  applicable_regulations:
    - regulation: "[Regulation name]"
      jurisdiction: "[Where applies]"
      applicability: "[Why this applies]"
  
  requirements_analysis:
    - regulation: "[Regulation]"
      requirements:
        - requirement_id: "[REQ-001]"
          requirement: "[Requirement text]"
          status: "[Compliant | Partial | Gap | N/A]"
          evidence: "[How demonstrated]"
          risk: "[High | Medium | Low]"
  
  gap_summary:
    critical:
      - gap: "[Gap description]"
        remediation: "[How to fix]"
        deadline: "[When needed]"
    
  remediation_plan:
    - action: "[Action]"
      owner: "[Who]"
      timeline: "[When]"
  
  approval:
    ready_for_deployment: "[Yes | No | Conditional]"
    conditions: ["[If conditional]"]
```

## Key Regulations

### EU AI Act
```yaml
eu_ai_act:
  risk_categories:
    prohibited:
      - "Social scoring"
      - "Manipulation of vulnerable groups"
    
    high_risk:
      - "Employment decisions"
      - "Credit decisions"
      - "Education access"
      requirements:
        - "Risk management system"
        - "Data governance"
        - "Technical documentation"
        - "Human oversight"
    
    limited_risk:
      - "Chatbots"
      requirements:
        - "Transparency (disclose AI use)"
```

## Assessment Checklist

- [ ] System profile documented
- [ ] Applicable regulations identified
- [ ] Requirements mapped
- [ ] Compliance assessed
- [ ] Gaps documented
- [ ] Remediation planned
- [ ] Approval obtained

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
