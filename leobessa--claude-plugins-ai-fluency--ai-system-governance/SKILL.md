---
name: ai-system-governance
description: Scale AI fluency beyond individuals through standards, guardrails, audit trails, and organizational policies that maintain quality and accountability. Use when this capability is needed.
metadata:
  author: leobessa
---

# Overview

**AI System Governance** is Layer 7 of AI fluency—the ability to scale fluency beyond the individual through organizational systems. This transforms personal competence into institutional capability.

**Core Principle:** Individual fluency doesn't scale. Systems do.

**Fluency Signal:** AI standards and guardrails are codified and followed by others.

---

## When to Use This Skill

- Introducing AI tools to teams or organizations
- When individual AI use varies wildly in quality
- When AI decisions need audit trails
- When building organizational AI policies
- When ensuring consistent AI quality across people

---

## Governance Framework

### The Four Pillars

#### Pillar 1: Standards

**What:** Documented expectations for AI use.

**Components:**
- **Quality standards**: What "good" AI output looks like
- **Process standards**: How AI tasks should be conducted
- **Documentation standards**: What must be recorded

**Example:**
```markdown
AI CONTENT STANDARDS

Quality:
- All facts must be verifiable from provided sources
- Claims about current events require date verification
- Quantitative claims need source citations

Process:
- Use approved prompt templates for customer-facing content
- Human review required before publication
- Revisions must be logged

Documentation:
- Prompt used (template + customizations)
- AI model and date
- Reviewer and approval
```

#### Pillar 2: Guardrails

**What:** Constraints that prevent misuse or errors.

**Types:**
- **Input guardrails**: What can/cannot go into AI
- **Output guardrails**: What must/must not appear in output
- **Process guardrails**: Required steps that cannot be skipped

**Example:**
```markdown
AI GUARDRAILS

Input restrictions:
- No customer PII in prompts without anonymization
- No proprietary competitor information
- No confidential financial data

Output requirements:
- Human review for any external communication
- Fact verification for quantitative claims
- Disclosure of AI assistance where required

Process requirements:
- Use only approved AI tools
- Log all AI interactions for audit
- Escalate to supervisor if uncertain
```

#### Pillar 3: Audit Trails

**What:** Records that enable review and accountability.

**What to capture:**
- What was asked (input/prompt)
- What was produced (output)
- What was done (decision/action)
- Who was involved (human actors)
- When it happened (timestamps)

**Example:**
```markdown
AUDIT LOG ENTRY

Date: [Timestamp]
Task: [What was being done]
User: [Who initiated]
AI Tool: [Which AI system]
Input: [Prompt or query - summarized]
Output: [Result - summarized]
Action: [What was done with output]
Review: [Who reviewed, if applicable]
Outcome: [Final result]
```

#### Pillar 4: Policies

**What:** Organizational rules governing AI use.

**Policy areas:**
- **Acceptable use**: What AI can/cannot be used for
- **Data handling**: How information flows to/from AI
- **Quality assurance**: Required verification steps
- **Accountability**: Who is responsible for what
- **Disclosure**: When AI use must be declared

---

## Governance Documentation

### AI Use Policy Template

```markdown
# AI Use Policy

## Purpose
[Why this policy exists]

## Scope
[Who and what this applies to]

## Acceptable Use
AI may be used for:
- [Permitted use 1]
- [Permitted use 2]

AI may NOT be used for:
- [Prohibited use 1]
- [Prohibited use 2]

## Data Handling
- Confidential data: [Rules]
- Personal data: [Rules]
- Proprietary data: [Rules]

## Quality Requirements
- [Requirement 1]
- [Requirement 2]

## Review and Approval
- [What requires review]
- [Who can approve]

## Documentation
- [What must be logged]
- [Where logs are kept]

## Accountability
- Individual responsibility: [Statement]
- Supervisor responsibility: [Statement]

## Violations
- [Consequences of policy violations]

## Questions
- Contact: [Who to ask]
```

### Quality Checklist Template

```markdown
# AI Output Quality Checklist

Before using AI-generated output, verify:

## Accuracy
□ Facts are correct (spot-check 3+ claims)
□ Numbers are accurate and properly sourced
□ No hallucinated citations or references

## Completeness
□ All required elements are present
□ No critical information is missing
□ Scope matches the request

## Appropriateness
□ Tone matches intended audience
□ No inappropriate content
□ Meets brand/style guidelines

## Compliance
□ Follows data handling rules
□ Meets disclosure requirements
□ Appropriate for intended use

Reviewer: _____________ Date: _____________
```

---

## Implementation Approach

### Phase 1: Assessment

Understand current state:
- How is AI being used today?
- What risks exist?
- What quality issues have occurred?
- What variations exist across people/teams?

### Phase 2: Standards Development

Create foundational documents:
1. Draft acceptable use policy
2. Define quality standards
3. Identify required guardrails
4. Design audit requirements

Involve stakeholders:
- Legal/compliance review
- Security review
- User input on practicality

### Phase 3: Implementation

Roll out systematically:
1. Communicate policies clearly
2. Provide training on standards
3. Implement technical guardrails where possible
4. Set up audit logging

### Phase 4: Enforcement and Iteration

Maintain governance:
1. Monitor compliance
2. Review audit logs periodically
3. Update policies based on learnings
4. Address violations consistently

---

## Practices

### Policy Gap Analysis

Review current AI use against governance needs:

```markdown
GAP ANALYSIS

| Area | Current State | Desired State | Gap | Priority |
|------|---------------|---------------|-----|----------|
| Standards | None documented | Written quality standards | High | P1 |
| Guardrails | Ad-hoc | Systematic checks | High | P1 |
| Audit | No logging | Full audit trail | Medium | P2 |
| Training | None | All users trained | Medium | P2 |
```

### Incident Review

When AI-related issues occur:

```markdown
INCIDENT REVIEW

Date: [When it happened]
Description: [What went wrong]
Impact: [What was the effect]
Root cause: [Why it happened]
Governance gap: [What policy/process failed]
Remediation: [What we'll change]
Owner: [Who is responsible]
```

### Governance Scorecard

Track governance health:

```markdown
GOVERNANCE SCORECARD - [Period]

| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| Policy compliance rate | 95% | [%] | [G/Y/R] |
| Audit log completeness | 100% | [%] | [G/Y/R] |
| Incidents this period | 0 | [N] | [G/Y/R] |
| Training completion | 100% | [%] | [G/Y/R] |
| Standards violations | 0 | [N] | [G/Y/R] |

Key issues: [Summary]
Actions: [What we'll do]
```

---

## Scaling Considerations

### Team Level

- Shared prompt templates
- Peer review processes
- Team-specific quality standards
- Local audit requirements

### Organization Level

- Enterprise AI policy
- Centralized tool approval
- Organization-wide training
- Compliance monitoring

### External Stakeholders

- Vendor AI policies
- Customer disclosure requirements
- Regulatory compliance
- Industry standards

---

## Assessment Criteria

**Layer 7 Complete When:**
- [ ] Has documented AI use standards
- [ ] Guardrails prevent common errors
- [ ] Audit trail captures AI interactions
- [ ] Policies are communicated and followed
- [ ] Others follow the governance system

---

## Common Governance Failures

### Failure 1: No Written Standards

**Wrong:** "Everyone knows what good looks like"
**Right:** Documented, specific quality criteria

### Failure 2: Unenforceable Guardrails

**Wrong:** "Don't put sensitive data in AI" (no mechanism)
**Right:** Technical controls + process checks + audit

### Failure 3: Audit Theater

**Wrong:** Logging everything but reviewing nothing
**Right:** Regular audit review with action on findings

### Failure 4: Policy Without Training

**Wrong:** Policy document exists but no one reads it
**Right:** Active training + accessible reference materials

---

## Related Skills

- [ai-workflow-integration](../ai-workflow-integration/SKILL.md) — Workflows that governance applies to
- [ai-evaluation-verification](../ai-evaluation-verification/SKILL.md) — Quality checks within governance
- [ai-strategic-fluency](../ai-strategic-fluency/SKILL.md) — Strategic context for governance

---

## Learn More

- [Policy Templates](references/policy-templates.md)
- [Audit Log Examples](references/audit-examples.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/leobessa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
