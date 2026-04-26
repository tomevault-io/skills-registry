---
name: audit-evidence-packager
description: Use when preparing evidence for internal or external audit of AI capabilities. Use when audit announced or during examination. Organizes documentation with executive summary, control evidence, and anticipated questions.
metadata:
  author: ethical-ai-syndicate
---

# Audit Evidence Packager

## Overview

Prepare comprehensive evidence packages that address auditor questions efficiently and present AI capabilities in an accessible, well-organized manner.

**Core principle:** Auditors don't need to understand AI deeply. They need to see that (1) you know what you're doing, (2) controls exist, and (3) controls are working.

## Package Structure

Organize evidence for auditor consumption, not internal convenience:

```
1. Executive Summary (start here)
2. System Documentation (how it works)
3. Policies and Procedures (how it's governed)
4. Control Evidence (proof controls work)
5. Operating Evidence (proof it's running)
6. Governance Evidence (proof of oversight)
7. Interview Preparation (for personnel)
```

## Output Format

```yaml
audit_evidence_package:
  capability: "[AI Capability Name]"
  audit_type: "[Internal Audit | External Audit | Regulatory Exam]"
  audit_scope: "[What's being examined]"
  package_date: "[Date]"
  package_owner: "[Who prepared]"
  evidence_period: "[Time period covered]"

executive_summary:
  for_auditors: |
    [2-3 paragraph summary in plain language]
    - What does this system do?
    - How long has it been operating?
    - Key statistics (volume, accuracy, issues)
    - Oversight mechanisms in place

  capability_in_plain_language: |
    [Explain how it works without technical jargon]
    [Use analogies if helpful]
    [Focus on what auditors care about: inputs, processing, outputs, controls]

evidence_inventory:
  category_1_system_documentation:
    - document: "[Document name]"
      location: "[Where to find it]"
      description: "[What it contains]"
      audit_relevance: "[Why auditor cares]"
      last_updated: "[Date]"

  category_2_policies_and_procedures:
    - document: "[Document name]"
      # ... same structure

  category_3_control_evidence:
    - document: "[Document name]"
      description: "[What it shows]"
      audit_relevance: "[Control it demonstrates]"
      samples_available: "[What samples can be pulled]"

  category_4_operating_evidence:
    - document: "[Document name]"
      # ... same structure

  category_5_governance:
    - document: "[Document name]"
      # ... same structure

audit_question_mapping:
  likely_questions:
    - question: "[Anticipated question]"
      evidence:
        - "[Document 1]"
        - "[Document 2]"
      prepared_response: |
        [Draft response with evidence references]

control_evidence_summary:
  control_N:
    control: "[Control name]"
    description: "[What it does]"
    evidence:
      - type: "[Evidence type]"
        showing: "[What it demonstrates]"
    effectiveness: "[Operating effectively | Issue identified]"

known_gaps_and_limitations:
  disclosed_proactively:
    - gap: "[Known limitation]"
      context: "[Why it exists]"
      mitigation: "[How addressed]"
      evidence: "[Supporting documentation]"

  areas_for_improvement:
    - area: "[Enhancement area]"
      status: "[Planned timeline]"
      evidence: "[Roadmap or plan]"

interview_preparation:
  key_personnel:
    - name: "[Name or role]"
      role: "[Responsibility]"
      topics: "[What they'll be asked about]"
      preparation: "[What they should review]"

  talking_points:
    - "[Key message 1]"
    - "[Key message 2]"

  topics_to_handle_carefully:
    - topic: "[Sensitive topic]"
      guidance: "[How to respond]"

document_request_response_plan:
  immediate_availability:
    - "[Documents ready now]"

  requires_preparation:
    - item: "[Document needing prep]"
      lead_time: "[How long]"
      owner: "[Who prepares]"

  sensitive_handling:
    - item: "[Sensitive document]"
      handling: "[Special procedures]"

package_completeness_checklist:
  - category: "[Category name]"
    status: "[Complete | Partial | Pending]"
```

## Executive Summary Best Practices

The executive summary is often all auditors read initially. Make it count:

### Do:
- Use plain business language
- State what the system does in one sentence
- Provide key operating statistics
- Acknowledge limitations proactively
- Reference where details can be found

### Don't:
- Use technical jargon (no "embeddings," "inference," "transformers")
- Oversell capabilities
- Hide problems (auditors will find them)
- Make it longer than 1 page

### Example Opening:

**Good:**
> "The Trade Surveillance system monitors approximately 200,000 trades daily for potential market manipulation. It has operated since January 2025, generating about 150 alerts per day for analyst review. Four matters were escalated to SAR consideration during the review period."

**Bad:**
> "The AI-powered surveillance platform leverages state-of-the-art transformer-based NLP models with attention mechanisms to perform real-time inference on trade flow data, achieving 0.85 AUC-ROC on held-out test sets."

## Control Evidence Organization

Auditors want to see controls exist AND operate effectively:

### Control Evidence Types

| Evidence Type | What It Shows | Example |
|---------------|---------------|---------|
| Design evidence | Control is designed | Policy document, procedure |
| Operating evidence | Control is functioning | Report showing control operated |
| Testing evidence | Control was verified | Validation report, test results |
| Exception evidence | Issues were caught | Exception log, remediation |

### For Each Control, Provide:

1. **What the control is** (description)
2. **Evidence it's designed** (policy/procedure)
3. **Evidence it's operating** (reports, logs)
4. **Evidence it's effective** (testing results)
5. **Any exceptions** (and remediation)

## Anticipated Questions Framework

Prepare for common audit questions:

### System Understanding
- "How does this AI system work?"
- "What decisions does it make or support?"
- "What data does it use?"

### Effectiveness
- "How do you know it's working correctly?"
- "What is the error rate?"
- "How do you validate accuracy?"

### Controls
- "What controls exist over the AI?"
- "How are exceptions handled?"
- "Who has oversight?"

### Limitations
- "What are the system's limitations?"
- "What can go wrong?"
- "Have there been any issues?"

### Changes
- "How has the system changed since deployment?"
- "How are changes tested?"
- "Who approves changes?"

### Governance
- "Who is responsible for this system?"
- "What oversight exists?"
- "How often is it reviewed?"

## Proactive Disclosure Strategy

Disclose limitations before auditors discover them:

### Why Proactive Disclosure Works
- Demonstrates awareness and control
- Prevents "gotcha" findings
- Shows mature risk management
- Builds auditor confidence

### What to Disclose
- Known limitations (with context)
- False positive/negative rates (with mitigation)
- Edge cases where system underperforms
- Planned enhancements

### How to Frame It
```yaml
limitation:
  what: "False positive rate is approximately 77%"
  context: "System is intentionally tuned for sensitivity over precision"
  why_acceptable: "Cost of missing true positive exceeds cost of reviewing false positive"
  mitigation: "Analyst review filters false positives efficiently"
  evidence: "Model documentation Section 5; tuning rationale memo"
```

## Interview Preparation

Prepare personnel before auditor interviews:

### Key Messages (Everyone Should Know)
- What the system does (one sentence)
- Key controls in place
- Who to defer to for technical details
- What NOT to speculate about

### Role-Specific Preparation

| Role | Topics | Preparation |
|------|--------|-------------|
| Business Owner | Purpose, value, oversight | Review statistics, committee minutes |
| Model Owner | Technical, validation, changes | Review model docs, validation reports |
| Operations | Day-to-day operation, issues | Review recent logs, exceptions |
| Compliance | Regulatory alignment, controls | Review procedures, control evidence |

### Topics to Handle Carefully

| Topic | Guidance |
|-------|----------|
| "Why so many false positives?" | Explain intentional sensitivity trade-off |
| "Have there been any failures?" | Be honest; show remediation |
| "How do you know you're not missing things?" | Point to validation methodology |
| Questions outside expertise | "Let me connect you with [right person]" |

## Document Request Response

Plan for efficient response:

### Immediate (Same Day)
- Executive summary
- System documentation
- Current procedures
- Standard reports

### Short Lead Time (1-3 Days)
- Custom date range reports
- Sample case files
- Specific log extracts

### Requires Coordination
- SAR-related materials (BSA Officer)
- Personnel files (HR)
- Legal privileged materials (Legal)

## Common Mistakes

| Mistake | Why It's Wrong | Do This Instead |
|---------|----------------|-----------------|
| Document dump | Auditors lose patience | Organize with index and summary |
| Technical language | Creates confusion | Translate to business language |
| Capabilities only | Auditors seek weaknesses | Disclose limitations proactively |
| Documents without context | Hard to navigate | Map documents to questions |
| Unprepared personnel | Inconsistent messages | Brief everyone on key messages |
| Reactive posture | Looks like hiding | Proactively offer information |

## Red Flags in Your Package

If your package has these, it's not ready:

- No executive summary in plain language
- Documents listed without audit relevance
- Control evidence missing effectiveness proof
- No limitation acknowledgment
- Anticipated questions not addressed
- Personnel not prepared for interviews
- No document request response plan

## Financial Services Context

Audit evidence for financial services AI requires:

### Regulatory Examination Awareness
- FINRA, SEC, OCC have specific expectations
- Know the examination modules that apply
- Anticipate regulator-specific questions

### Control Focus
- Auditors verify controls, not just capability
- Operating effectiveness evidence is critical
- Exception handling demonstrates maturity

### Plain Language Imperative
- Examiners may not be technical
- AI must be explained accessibly
- Analogies help ("like a spam filter for trades")

### Proactive Disclosure Culture
- Better to surface issues yourself
- Shows mature risk management
- Prevents adversarial dynamic

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
