---
name: governance-alignment-translator
description: Use when preparing AI capability documentation for risk committee, compliance, or audit review. Use after technical development is complete. Produces governance-friendly documentation with regulatory mapping and stakeholder-specific summaries.
metadata:
  author: ethical-ai-syndicate
---

# Governance Alignment Translator

## Overview

Translate technical AI capability documentation into language governance bodies understand. The goal is documentation that enables informed approval decisions by risk committees, compliance officers, and auditors.

**Core principle:** Governance doesn't need to understand HOW it works technically. They need to understand WHAT it does, WHAT could go wrong, and HOW risks are controlled.

## Governance Tier Classification

Classify capability before writing documentation:

| Tier | Criteria | Governance Requirements |
|------|----------|------------------------|
| Tier 1 - High Impact | Customer-facing decisions, regulatory reporting, material financial impact | Full MRM review, Risk Committee approval, quarterly attestation |
| Tier 2 - Operational | Internal process automation, decision support, efficiency tools | MRM notification, Operations leadership approval, annual review |
| Tier 3 - Low Risk | Analytics, reporting, non-decision tools | Technology approval, documented procedures |

```yaml
tier_classification:
  tier: "Tier 2 - Operational"
  rationale: |
    - Automates internal matching process (not customer-facing)
    - Supports human decision, doesn't replace judgment
    - Settlement impact is detectable and reversible
  mrm_requirement: "Model Risk notification + documentation"
```

## Output Format

```yaml
governance_package:
  capability: "[Name]"
  tier: "[Tier 1/2/3]"
  tier_rationale: "[Why this tier]"
  owner: "[Business owner]"
  prepared_for: "[Governance body]"

executive_summary:
  business_description: |
    [What it does in plain business terms - no technical jargon]

  business_case: |
    [Why we're doing this - efficiency, risk reduction, etc.]
    [Quantified benefits where possible]

  risk_comparison_to_status_quo: |
    [Current process also has risks - compare]
    [Is AI risk higher, lower, or different?]

  decision_requested: |
    [Specific approval being sought]

risk_profile:
  risk_category: "[Operational/Credit/Market/Compliance/etc.]"

  identified_risks:
    - risk: "[Description]"
      impact: "[HIGH/MEDIUM/LOW] - [$ range or description]"
      likelihood_before_controls: "[HIGH/MEDIUM/LOW]"
      likelihood_after_controls: "[HIGH/MEDIUM/LOW]"
      residual_rating: "[Result]"

  comparison_to_manual: |
    [Manual process error rate vs AI error rate]
    [Which risks are reduced, which are introduced?]

control_framework:
  controls:
    - control_id: "[C1]"
      control: "[Description]"
      risk_addressed: "[Which risk]"
      owner: "[Named role]"
      monitoring: "[How verified]"
      effectiveness: "[Design: Strong/Moderate/Weak, Operating: Tested/Not tested]"

  control_gaps:
    - "[Any gaps identified]"

regulatory_alignment:
  applicable_regulations:
    - regulation: "[Citation]"
      specific_requirements: |
        [What exactly does regulation require?]
      compliance_approach: |
        [How does this capability comply?]
      evidence: "[What documentation exists]"

  model_risk_management:
    mrm_tier: "[Tier 1/2/3 per OCC 2011-12]"
    tier_rationale: "[Why this tier]"
    documentation_status: "[Submitted/Approved/N/A]"
    validation_approach: "[How model is validated]"

governance_requirements:
  approvals_required:
    - body: "[Name]"
      status: "[Pending/Approved/N/A]"
      condition_link: "[Which approval condition]"

  ongoing_oversight:
    - mechanism: "[What oversight]"
      frequency: "[How often]"
      owner: "[Who]"

  approval_conditions:
    - condition: "[Specific condition]"
      risk_addressed: "[Which risk this mitigates]"
      verification: "[How compliance verified]"

audit_considerations:
  audit_trail:
    - "[What is logged]"

  evidence_available:
    - "[What can be examined]"

  examination_approach: |
    [How auditors would test this capability]

stakeholder_summaries:
  cro_one_pager: |
    [Risk-focused summary for CRO - 1 page max]

  compliance_checklist:
    - requirement: "[Regulation/requirement]"
      status: "[Compliant/In progress/Gap]"
      evidence: "[Documentation reference]"

  audit_work_program:
    - test: "[What to test]"
      method: "[How to test]"
      expected_evidence: "[What to look for]"
```

## Translation Rules

### Technical → Governance Language

| Technical | Governance |
|-----------|------------|
| BERT/transformer/model | "AI system" or "automated system" |
| Confidence score | "Certainty measure (0-100%)" |
| False positive | "Incorrect match" |
| Inference latency | "Processing time" |
| Training data | "Historical examples the system learned from" |
| Fine-tuned | "Customized for our data" |
| Embeddings | "Internal representation" (or omit) |
| Cosine similarity | "Matching algorithm" (or omit) |

### Risk Translation

| Technical Risk | Governance Risk |
|----------------|-----------------|
| Model accuracy 97.2% | "~3% of items may require correction" |
| False positive rate 0.4% | "~20 per day incorrectly matched, caught in reconciliation" |
| P95 latency 28s | "95% complete within 30 seconds" |

## Risk Comparison to Status Quo

**Always compare to current state:**

```yaml
risk_comparison_to_status_quo:
  current_manual_process:
    error_rate: "~2-5% (variable by analyst)"
    failure_modes:
      - "Fatigue-related errors in afternoon"
      - "Inconsistent interpretation across staff"
      - "Peak volume creates backlog"

  proposed_ai_process:
    error_rate: "~3% (consistent)"
    failure_modes:
      - "Edge cases (split fills, new formats)"
      - "Extraction errors on poor quality documents"

  net_risk_change: |
    AI reduces variability and peak-volume risk.
    AI introduces edge case failure modes.
    Overall: Similar error rate with better consistency.
```

## MRM Tier Classification

Per OCC 2011-12 Supervisory Guidance on Model Risk Management:

| MRM Tier | Criteria | Requirements |
|----------|----------|--------------|
| Tier 1 | Material financial impact, regulatory reporting, customer decisions | Full validation, ongoing monitoring, annual revalidation |
| Tier 2 | Operational impact, internal decisions, moderate complexity | Documentation, periodic validation, performance monitoring |
| Tier 3 | Low impact, simple models, minimal risk | Documentation, basic testing |

```yaml
model_risk_management:
  mrm_tier: "Tier 2"
  tier_rationale: |
    - Operational impact (settlement matching)
    - Not customer-facing decision
    - Error detectable in reconciliation
    - Moderate model complexity
  documentation_status: "Submitted - pending review"
```

## Approval Condition Mapping

**Every condition must link to a risk:**

```yaml
approval_conditions:
  - condition: "30-day pilot at 50% volume"
    risk_addressed: "Unknown production behavior"
    verification: "Accuracy metrics during pilot"

  - condition: "Manual fallback tested quarterly"
    risk_addressed: "System unavailability"
    verification: "Documented test results"

  - condition: "Accuracy monitoring with 95% threshold"
    risk_addressed: "Model degradation"
    verification: "Real-time dashboard + alert logs"
```

## Stakeholder-Specific Outputs

### CRO One-Pager
- What is it? (2 sentences)
- What's the risk? (top 3 risks with ratings)
- How is risk controlled? (key controls)
- What approval is needed? (specific ask)
- What's the residual risk? (after controls)

### Compliance Checklist
| Requirement | Status | Evidence |
|-------------|--------|----------|
| FINRA 4511 | ✓ Compliant | Decision log design doc |
| SEC 17a-4 | ✓ Compliant | WORM storage cert |
| OCC 2011-12 | ○ In Progress | MRM submission |

### Audit Work Program
| Test Objective | Procedure | Evidence Required |
|----------------|-----------|-------------------|
| Verify accuracy | Recalculate from sample | Raw data + calculations |
| Test routing | Review low-confidence items | Queue logs |
| Validate controls | Walk through each control | Control documentation |

## Common Mistakes

| Mistake | Why It's Wrong | Do This Instead |
|---------|----------------|-----------------|
| Technical jargon | Governance doesn't understand ML | Plain business language |
| AI risks only | Ignores current state also has risks | Compare to manual process |
| Generic "compliant" | Doesn't prove compliance | Cite specific requirements |
| One document for all | Different audiences need different depth | Stakeholder-specific summaries |
| Conditions without rationale | Why these specific conditions? | Link each to a risk |
| MRM mentioned but not classified | Unclear what's required | Explicit MRM tier |

## Red Flags in Your Output

If your documentation has these, it's not ready:

- Technical terms without translation
- No tier classification rationale
- Risk comparison to status quo missing
- Regulations cited without specific requirements
- Controls without effectiveness assessment
- Conditions without risk mapping
- No stakeholder-specific summaries

## Financial Services Context

Financial services governance translation requires:

### Regulatory Precision
- FINRA Rule 4511 - books and records (6 year retention)
- SEC Rule 17a-4 - electronic records preservation
- OCC 2011-12 - model risk management
- SOX 404 - internal controls (if public company)

### Risk Committee Expectations
- Impact/likelihood matrix format
- Residual risk ratings
- Control owner accountability
- Ongoing oversight mechanisms

### Audit Readiness
- What evidence exists
- How to test controls
- What sampling approach is appropriate
- What findings would trigger concerns

## Governance Package Checklist

Before submission:

- [ ] Tier classification with rationale
- [ ] Business description in plain language
- [ ] Risk comparison to current process
- [ ] Control framework with owners and effectiveness
- [ ] Regulatory mapping with specific requirements
- [ ] MRM tier classification
- [ ] Approval conditions linked to risks
- [ ] Stakeholder-specific summaries (CRO, Compliance, Audit)
- [ ] Audit trail and evidence specified

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
