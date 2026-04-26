---
name: ai-incident-responder
description: Use when AI systems fail or behave unexpectedly. Use after incident detected. Produces incident classification, immediate response actions, rollback procedures, and post-mortem templates.
metadata:
  author: ethical-ai-syndicate
---

# AI Incident Responder

## Overview

Handle AI system failures systematically with structured response procedures. Classify incidents, execute immediate mitigations, and conduct thorough post-mortems.

**Core principle:** AI incidents are different—they can be subtle, hard to detect, and have delayed impacts. Respond quickly but investigate thoroughly.

## When to Use

- Model producing unexpected outputs
- Accuracy degradation detected
- User complaints about AI behavior
- Bias or fairness issue discovered
- System outage or performance degradation

## Output Format

```yaml
ai_incident:
  incident_id: "[INC-YYYY-NNNN]"
  reported: "[YYYY-MM-DD HH:MM]"
  reporter: "[Name/System]"
  
  classification:
    severity: "[Critical | High | Medium | Low]"
    type: "[Output Quality | Bias | Performance | Availability | Security]"
    affected_systems: ["[System 1]", "[System 2]"]
    affected_users: "[Scope of impact]"
    customer_facing: [true | false]
  
  detection:
    how_detected: "[Monitoring | User report | Audit | etc.]"
    detection_delay: "[Time from occurrence to detection]"
    detection_gap: "[Why wasn't it caught sooner]"
  
  immediate_response:
    status: "[Investigating | Mitigating | Resolved | Monitoring]"
    actions_taken:
      - time: "[HH:MM]"
        action: "[What was done]"
        by: "[Who]"
        result: "[Outcome]"
    
    mitigation:
      approach: "[Rollback | Fallback | Disable | Throttle]"
      implemented: "[YYYY-MM-DD HH:MM]"
      effectiveness: "[Resolved | Partial | Ongoing]"
  
  investigation:
    root_cause: "[What caused the incident]"
    contributing_factors:
      - "[Factor 1]"
      - "[Factor 2]"
    
    timeline:
      - time: "[When]"
        event: "[What happened]"
    
    evidence:
      - type: "[Logs | Metrics | User reports | Model outputs]"
        location: "[Where to find]"
        summary: "[Key findings]"
  
  resolution:
    fix_applied: "[Description of fix]"
    fix_deployed: "[YYYY-MM-DD HH:MM]"
    verification: "[How we confirmed fix works]"
    
  impact:
    users_affected: "[Number or scope]"
    duration: "[Time from start to resolution]"
    business_impact: "[Revenue, reputation, compliance]"
    data_impact: "[Any data affected]"
  
  post_mortem:
    lessons_learned:
      - "[Lesson 1]"
    
    action_items:
      - action: "[Preventive action]"
        owner: "[Who]"
        due: "[When]"
        status: "[Open | Complete]"
    
    process_improvements:
      - "[What to change in how we work]"
  
  communication:
    internal_updates: ["[When and to whom]"]
    customer_communication: "[If applicable]"
    regulatory_notification: "[If required]"
```

## Incident Severity Matrix

| Severity | Criteria | Response Time | Escalation |
|----------|----------|---------------|------------|
| **Critical** | Customer-facing AI completely wrong, bias issue, data breach | 15 min | Immediate exec notification |
| **High** | Significant accuracy drop, partial outage | 1 hour | Manager notification |
| **Medium** | Noticeable degradation, edge case failures | 4 hours | Team lead awareness |
| **Low** | Minor issues, single user reports | 24 hours | Standard ticket |

## AI-Specific Incident Types

### Output Quality
```yaml
symptoms:
  - "Model producing nonsense/hallucinations"
  - "Accuracy below acceptable threshold"
  - "Inconsistent outputs for same input"

immediate_actions:
  - "Enable fallback/human review"
  - "Increase logging on affected flows"
  - "Check for data drift or input anomalies"
```

### Bias/Fairness
```yaml
symptoms:
  - "Disparate outcomes across protected groups"
  - "User complaints about discrimination"
  - "Audit findings"

immediate_actions:
  - "Disable automated decisions pending review"
  - "Engage legal/compliance"
  - "Preserve evidence for investigation"
```

### Performance
```yaml
symptoms:
  - "Latency exceeds SLA"
  - "Timeouts on inference"
  - "Resource exhaustion"

immediate_actions:
  - "Scale resources if possible"
  - "Enable request throttling"
  - "Route to backup system"
```

## Response Playbook

### Phase 1: Detect & Triage (0-15 min)
```yaml
steps:
  - "Acknowledge incident, assign owner"
  - "Classify severity and type"
  - "Notify stakeholders per severity"
  - "Begin impact assessment"
```

### Phase 2: Mitigate (15 min - 2 hours)
```yaml
steps:
  - "Implement immediate mitigation"
  - "Verify mitigation effectiveness"
  - "Continue customer communication"
  - "Document actions taken"
```

### Phase 3: Investigate (Ongoing)
```yaml
steps:
  - "Gather evidence (logs, metrics, outputs)"
  - "Reproduce issue if possible"
  - "Identify root cause"
  - "Identify contributing factors"
```

### Phase 4: Resolve (Once cause known)
```yaml
steps:
  - "Develop and test fix"
  - "Deploy fix with appropriate review"
  - "Verify resolution"
  - "Remove mitigations if appropriate"
```

### Phase 5: Post-Mortem (Within 5 days)
```yaml
steps:
  - "Document full timeline"
  - "Identify lessons learned"
  - "Create preventive action items"
  - "Share learnings with team"
```

## Rollback Decision Tree

```
Is the AI producing harmful outputs?
├── YES → Immediate rollback or disable
│
└── NO → Is accuracy significantly degraded?
         ├── YES → Can users tolerate degraded experience?
         │         ├── YES → Mitigate, keep running, fix forward
         │         └── NO → Rollback to previous version
         │
         └── NO → Monitor closely, investigate root cause
```

## Post-Mortem Template

```markdown
# Incident Post-Mortem: [INC-YYYY-NNNN]

## Summary
[One paragraph description]

## Impact
- Duration: [X hours]
- Users affected: [N]
- Business impact: [Description]

## Timeline
| Time | Event |
|------|-------|
| [Time] | [Event] |

## Root Cause
[Detailed explanation]

## What Went Well
- [Positive 1]

## What Went Poorly
- [Negative 1]

## Action Items
| Action | Owner | Due | Status |
|--------|-------|-----|--------|
| [Action] | [Name] | [Date] | [Status] |

## Lessons Learned
- [Lesson 1]
```

## Checklist

During incident:
- [ ] Incident classified and logged
- [ ] Severity determined
- [ ] Owner assigned
- [ ] Stakeholders notified
- [ ] Mitigation in place
- [ ] Impact documented

After resolution:
- [ ] Root cause identified
- [ ] Fix verified
- [ ] Post-mortem completed
- [ ] Action items assigned
- [ ] Learnings shared

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
