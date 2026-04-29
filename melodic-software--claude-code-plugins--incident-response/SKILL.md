---
name: incident-response
description: Use when designing incident management processes, creating runbooks, or establishing on-call practices. Covers incident lifecycle, communication, and postmortems.
metadata:
  author: melodic-software
---

# Incident Response

Patterns and practices for effective incident management, from detection through postmortem.

## When to Use This Skill

- Designing incident response processes
- Creating incident runbooks
- Establishing on-call rotations
- Running effective postmortems
- Improving mean time to recovery (MTTR)

## Incident Lifecycle

```text
┌─────────────────────────────────────────────────────────┐
│                  INCIDENT LIFECYCLE                      │
│                                                          │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐   │
│  │ Detect  │─►│ Respond │─►│ Recover │─►│ Learn   │   │
│  └─────────┘  └─────────┘  └─────────┘  └─────────┘   │
│       │            │            │            │          │
│       ▼            ▼            ▼            ▼          │
│   Alerting    Triage &     Mitigation  Postmortem     │
│   Monitoring  Diagnosis    Remediation  Action Items   │
└─────────────────────────────────────────────────────────┘
```

### Key Metrics

```text
MTTD - Mean Time to Detect
└── Time from incident start to detection

MTTA - Mean Time to Acknowledge
└── Time from alert to human acknowledgment

MTTR - Mean Time to Recover
└── Time from detection to resolution

MTTF - Mean Time to Failure
└── Time between incidents (reliability)

Goal: Minimize MTTD + MTTA + MTTR
```

## Incident Severity

### Severity Levels

```text
SEV 1 - Critical
├── Complete outage
├── Data loss or security breach
├── All/most users affected
├── Response: Immediate (24/7)
└── Example: Production database down

SEV 2 - High
├── Major functionality impaired
├── Significant user impact
├── Workaround may exist
├── Response: Urgent (business hours++)
└── Example: Payment processing degraded

SEV 3 - Medium
├── Partial functionality affected
├── Limited user impact
├── Workaround available
├── Response: Normal priority
└── Example: Report generation slow

SEV 4 - Low
├── Minor issue
├── Minimal user impact
├── Response: Best effort
└── Example: UI cosmetic bug
```

### Severity Matrix

```text
                    User Impact
                 Low   Medium   High
Scope    ├─────────────────────────────┤
Wide     │  SEV3   SEV2    SEV1       │
Medium   │  SEV4   SEV3    SEV2       │
Limited  │  SEV4   SEV4    SEV3       │
         └─────────────────────────────┘
```

## Incident Roles

### Core Roles

```text
Incident Commander (IC)
├── Owns the incident end-to-end
├── Makes decisions and delegates
├── Controls incident channel
├── Does NOT debug (coordinates)
└── Focus: Big picture, communication

Tech Lead
├── Leads technical investigation
├── Coordinates technical responders
├── Makes technical decisions
├── Reports to IC
└── Focus: Root cause, fix

Communications Lead
├── Handles external communication
├── Updates status page
├── Manages customer notifications
├── Reports to IC
└── Focus: Stakeholder updates

Scribe
├── Documents timeline
├── Records decisions and actions
├── Captures important information
├── Reports to IC
└── Focus: Documentation
```

### Role Handoffs

```text
Handoff protocol:
1. IC: "I'm handing IC to [Name]"
2. New IC: "I'm taking IC. Current status is..."
3. IC: "Confirmed, [Name] is now IC"

Handoff when:
- Shift ends
- Fatigue sets in
- Expertise needed
- Escalation required
```

## Incident Communication

### Communication Channels

```text
Internal:
┌─────────────────────────────────────────────────────────┐
│ #incident-YYYY-MM-DD-topic                              │
│ - All incident communication here                       │
│ - Pinned: Current status, timeline, roles              │
│ - Bridge call link for voice                            │
└─────────────────────────────────────────────────────────┘

External:
- Status page (status.example.com)
- Customer emails
- Social media (if needed)
- Support channels
```

### Status Update Template

```text
[TIME] Incident Update - [TITLE]

Current Status: [Investigating|Identified|Monitoring|Resolved]

Impact: [What users are experiencing]

What we know:
- [Key facts]

What we're doing:
- [Current actions]

Next update: [Time or "as soon as we learn more"]
```

### Update Cadence

```text
SEV 1: Every 15-30 minutes
SEV 2: Every 30-60 minutes
SEV 3: Every 1-2 hours
SEV 4: As needed

Also update when:
- Status changes
- Major new information
- Actions taken
- Resolution achieved
```

## Incident Response Process

### Step 1: Detection & Alert

```text
Triggers:
- Automated alerting
- User reports
- Internal discovery

First responder actions:
1. Acknowledge alert
2. Assess initial impact
3. Declare incident if needed
4. Page additional responders
5. Open incident channel
```

### Step 2: Triage & Mobilize

```text
Triage questions:
- What is the user impact?
- How many users affected?
- Is there a workaround?
- What's the severity?

Mobilize:
1. Page appropriate responders
2. Establish roles (IC, Tech Lead, Comms)
3. Start incident channel
4. Begin timeline documentation
```

### Step 3: Investigate & Diagnose

```text
Investigation approach:
1. What changed recently?
   └── Deployments, config, infrastructure

2. What do metrics/logs show?
   └── Error rates, latency, traces

3. What's the blast radius?
   └── Which services, which users

4. What are the hypotheses?
   └── List, prioritize, test

Common commands:
- "Checking [system] now"
- "Theory: [hypothesis]"
- "Found: [discovery]"
- "Need: [resource/access]"
```

### Step 4: Mitigate

```text
Mitigation strategies:
1. Rollback
   └── Revert recent changes

2. Failover
   └── Switch to backup/replica

3. Scale
   └── Add capacity

4. Disable
   └── Turn off affected feature

5. Hotfix
   └── Deploy targeted fix

Priority: Restore service first, root cause later
```

### Step 5: Resolve & Verify

```text
Resolution checklist:
□ Service restored
□ Metrics normalized
□ User-facing impact ended
□ Monitoring in place for recurrence
□ Temporary mitigations documented

Verification:
- Check key SLIs
- Test user flows
- Monitor for 15-30 minutes
- Confirm with affected teams
```

### Step 6: Close & Learn

```text
Closure:
1. Declare incident resolved
2. Final status update
3. Schedule postmortem
4. Assign postmortem owner
5. Close incident channel (archive)

Timeline:
- Postmortem doc: Within 24-48 hours
- Postmortem meeting: Within 5 business days
- Action items: Tracked to completion
```

## On-Call Practices

### On-Call Structure

```text
Primary: First responder to alerts
Secondary: Backup if primary unavailable
Escalation: Manager/senior for major incidents

Rotation options:
- Weekly rotation
- Follow-the-sun (multiple timezones)
- Split shifts (day/night)

Off-hours policy:
- What's page-worthy?
- What can wait?
- Compensation for off-hours
```

### On-Call Responsibilities

```text
During shift:
- Respond to alerts within SLA
- Triage and resolve or escalate
- Document actions taken
- Hand off to next shift

Handoff includes:
- Open alerts/incidents
- Recent incidents
- Known issues
- Scheduled changes
```

### Alert Hygiene

```text
Good alert:
- Actionable
- Urgent
- User-impacting
- Clear resolution path

Alert anti-patterns:
- "Somebody should look at this"
- Duplicate alerts
- Non-actionable information
- Crying wolf (frequent false positives)

Regular review:
- Which alerts fired?
- Which were actionable?
- Which were noise?
- What's missing?
```

## Runbooks

### Runbook Structure

```text
# Runbook: [Alert Name]

## Overview
What this alert means and why it matters.

## Impact
What users experience when this fires.

## Investigation Steps
1. Check [metric/log/dashboard]
2. Look for [specific pattern]
3. Verify [component status]

## Mitigation Steps
1. If [condition], do [action]
2. If [condition], do [action]
3. Escalate if [condition]

## Rollback Procedure
How to undo changes if needed.

## Contacts
- Service owner: [name]
- Escalation: [name/team]

## Related Links
- Dashboard: [link]
- Logs: [query]
- Service docs: [link]
```

### Runbook Maintenance

```text
Keep runbooks current:
- Update after incidents
- Review quarterly
- Test procedures
- Remove stale content

Runbook location:
- Linked from alert
- Searchable/discoverable
- Version controlled
```

## Postmortems

### Blameless Culture

```text
Blameless postmortem:
- Focus on systems, not individuals
- Assume people acted rationally
- Look for contributing factors
- Improve systems and processes

Not blameless: "John should have..."
Blameless: "The system allowed..."
```

### Postmortem Template

```text
# Incident Postmortem: [Title]

Date: [Date]
Duration: [Start - End]
Severity: [SEV level]
Authors: [Names]

## Summary
One paragraph summary of what happened.

## Impact
- Users affected: [Number/percentage]
- Duration: [Time]
- Revenue impact: [If applicable]

## Timeline
[Time] - Event
[Time] - Action taken
[Time] - Resolution

## Root Cause
What actually caused the incident.

## Contributing Factors
What made it worse or harder to resolve.

## What Went Well
- [Positive observations]

## What Could Be Improved
- [Improvement areas]

## Action Items
| Action | Owner | Due Date | Status |
|--------|-------|----------|--------|
| [Action] | [Name] | [Date] | [Status] |

## Lessons Learned
Key takeaways for the organization.
```

### Action Item Types

```text
Prevent: Stop this from happening again
Detect: Find it faster next time
Mitigate: Reduce impact when it happens
Document: Improve runbooks/documentation

Priority:
1. High-impact, low-effort
2. Required for safety
3. Reduces toil
4. Nice to have
```

## Best Practices

```text
1. Declare incidents early
   When in doubt, declare

2. Focus on mitigation first
   Root cause analysis later

3. Communicate frequently
   Silence breeds anxiety

4. Document as you go
   Don't rely on memory

5. Practice with game days
   Drill before real incidents

6. Blameless postmortems
   Systems fail, not people

7. Track action items
   Complete what you commit

8. Regular on-call review
   Improve the on-call experience
```

## Related Skills

- `slo-sli-error-budget` - SLOs and alerting
- `observability-patterns` - Using observability in incidents
- `chaos-engineering-fundamentals` - Proactive resilience testing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
