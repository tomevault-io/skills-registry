---
name: definition-of-done-enforcer
description: Use when validating completed work against standards. Use before accepting stories as done. Produces DoD checklist evaluation, gap report, and completion recommendations.
metadata:
  author: ethical-ai-syndicate
---

# Definition of Done Enforcer

## Overview

Systematically validate that completed work meets the team's Definition of Done. Ensures consistent quality and prevents "almost done" from becoming "done."

**Core principle:** Done means done—not "done except for..." Consistent DoD enforcement prevents quality erosion.

## When to Use

- Reviewing stories for sprint acceptance
- Pre-demo validation
- Release readiness check
- Onboarding team to quality standards

## Output Format

```yaml
dod_evaluation:
  story_id: "[Story ID]"
  story_title: "[Title]"
  evaluator: "[Name]"
  evaluation_date: "[YYYY-MM-DD]"
  
  checklist:
    code_quality:
      - item: "Code reviewed and approved"
        status: "[Pass | Fail | N/A]"
        notes: "[Details if not passing]"
      - item: "No known bugs"
        status: "[Pass | Fail | N/A]"
        notes: ""
        
    testing:
      - item: "Unit tests written and passing"
        status: "[Pass | Fail | N/A]"
        coverage: "[X%]"
      - item: "Integration tests passing"
        status: "[Pass | Fail | N/A]"
      - item: "Manual QA completed"
        status: "[Pass | Fail | N/A]"
        
    deployment:
      - item: "Deployed to staging"
        status: "[Pass | Fail | N/A]"
      - item: "Verified in staging"
        status: "[Pass | Fail | N/A]"
        
    documentation:
      - item: "User documentation updated"
        status: "[Pass | Fail | N/A]"
      - item: "Technical documentation updated"
        status: "[Pass | Fail | N/A]"
        
    acceptance:
      - item: "Acceptance criteria verified"
        status: "[Pass | Fail | N/A]"
        criteria_results:
          - criterion: "[AC-1]"
            status: "[Pass | Fail]"
      - item: "PO acceptance"
        status: "[Pass | Fail | Pending]"
  
  summary:
    total_items: "[N]"
    passed: "[N]"
    failed: "[N]"
    not_applicable: "[N]"
    overall_status: "[Ready | Not Ready]"
  
  gaps:
    - item: "[DoD item not met]"
      severity: "[Blocking | Significant | Minor]"
      remediation: "[What's needed to pass]"
      effort: "[Estimated time]"
      owner: "[Who will fix]"
  
  recommendation:
    accept: [true | false]
    conditions: ["[If conditional acceptance]"]
    next_steps: ["[Required actions]"]
```

## Standard DoD Template

### Universal DoD Items
```yaml
definition_of_done:
  code:
    - "Code compiles without errors"
    - "Code follows team style guidelines"
    - "Code reviewed by at least one peer"
    - "No new technical debt without documentation"
    
  testing:
    - "Unit tests written for new code"
    - "All tests pass (unit, integration, e2e)"
    - "Test coverage meets threshold"
    - "Edge cases tested per acceptance criteria"
    
  quality:
    - "No critical or high severity bugs"
    - "Performance meets requirements"
    - "Security review passed (if applicable)"
    
  deployment:
    - "Deployed to staging environment"
    - "Smoke tests pass in staging"
    - "Feature flag configured (if applicable)"
    
  documentation:
    - "User-facing docs updated"
    - "API documentation updated (if applicable)"
    - "Runbook updated (if applicable)"
    
  acceptance:
    - "All acceptance criteria met"
    - "Demonstrated to PO"
    - "PO accepted"
```

### Context-Specific Additions

#### Regulated Environment
```yaml
regulated_additions:
  - "Audit logging implemented"
  - "Compliance review passed"
  - "Change request approved"
  - "Evidence captured for audit"
```

#### Customer-Facing
```yaml
customer_facing_additions:
  - "Accessibility standards met"
  - "Cross-browser testing completed"
  - "Mobile responsive verified"
  - "Localization reviewed"
```

## Evaluation Process

### Quick Check (5 min)
```yaml
quick_check:
  - "Tests passing in CI?"
  - "PR approved and merged?"
  - "Deployed to staging?"
  - "Demo shown to PO?"
```

### Full Evaluation (15-30 min)
```yaml
full_evaluation:
  - Review all checklist items
  - Verify evidence for each
  - Note any gaps or concerns
  - Discuss with developer if unclear
  - Document recommendation
```

## Gap Severity

| Severity | Definition | Action |
|----------|------------|--------|
| **Blocking** | Cannot accept without fix | Must remediate before done |
| **Significant** | Should fix before release | Accept with commitment to fix |
| **Minor** | Nice to fix, not required | Accept, track as improvement |

## Common Gaps

| Gap | Typical Cause | Prevention |
|-----|---------------|------------|
| Missing tests | Time pressure | Include test time in estimates |
| Docs outdated | Forgotten | Add to PR checklist |
| Staging not verified | Environment issues | Automate verification |
| AC not all met | Scope creep or oversight | Review AC before marking done |

## Enforcement Approaches

### Collaborative (Recommended)
```yaml
approach:
  - Developer self-certifies DoD
  - Peer reviews evidence
  - PO verifies acceptance criteria
  - Disagreements discussed, not dictated
```

### Automated (Partial)
```yaml
automated_checks:
  - CI/CD gates (tests, coverage, linting)
  - Deployment verification
  - Documentation link check
  
manual_checks:
  - Acceptance criteria verification
  - PO acceptance
  - Edge case quality
```

## Handling "Almost Done"

### Symptoms
- "Just needs testing"
- "Waiting for review"
- "Works on my machine"
- "Docs will be done later"

### Response
```yaml
response:
  acknowledge: "I see significant work is done"
  clarify: "Our DoD requires [missing items] before we call it done"
  plan: "What do you need to complete these items?"
  support: "How can I help unblock?"
```

## DoD Evolution

### When to Update DoD
```yaml
update_triggers:
  - "Repeated quality issues from same gap"
  - "Team capability improvement"
  - "New compliance requirement"
  - "Retrospective identifies need"
```

### Update Process
```yaml
update_process:
  - "Propose change in retrospective"
  - "Discuss effort impact"
  - "Team agrees to adoption"
  - "Update documentation"
  - "Communicate to stakeholders"
```

## Evaluation Checklist

- [ ] All DoD items evaluated
- [ ] Evidence reviewed (not just self-reported)
- [ ] Gaps documented with severity
- [ ] Remediation plan for gaps
- [ ] Recommendation is clear
- [ ] Developer and PO aligned

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
