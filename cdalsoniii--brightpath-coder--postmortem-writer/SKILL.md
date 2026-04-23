---
name: postmortem-writer
description: Write blameless postmortem documents from incident data Use when this capability is needed.
metadata:
  author: cdalsoniii
---

# Postmortem Writer Skill

Write blameless postmortem documents from incident data.

## Trigger Conditions
- Incident resolved with severity S1 or S2
- Scheduled postmortem review
- User invokes with "write postmortem" or "incident review"

## Input Contract
- **Required:** Incident timeline (detection, mitigation, resolution)
- **Required:** Impact assessment (users, revenue, SLA)
- **Optional:** Contributing factors, action items

## Output Contract
- Postmortem document with timeline, analysis, and action items
- SMART action items with owners and deadlines
- Recommendations for systemic improvements

## Tool Permissions
- **Read:** Incident logs, deploy logs, alerts, metrics, communication records
- **Write:** Postmortem documents in `docs/postmortems/`
- **Search:** Prior postmortems for recurrence patterns

## Execution Steps
1. Reconstruct timeline from system data (not memory)
2. Identify contributing factors (plural, not single root cause)
3. Apply Five Whys with guardrails (stop at system gaps, not people)
4. Assess detection gap (time from failure to detection)
5. Evaluate mitigation effectiveness
6. Create SMART action items
7. Check for recurrence patterns against past postmortems
8. Write document in standard template

## Success Criteria
- Timeline built from system data, not memory
- Contributing factors identified (not single root cause)
- Action items are SMART (Specific, Measurable, Assigned, Realistic, Time-bound)
- Recurrence patterns checked

## Escalation Rules
- Escalate if similar incident occurred in last 6 months
- Escalate if action items require cross-team coordination
- Escalate if incident had regulatory implications

## Example Invocations

**Input:** "Write postmortem for the 2-hour payment outage on Feb 3"

**Output:** Postmortem: Duration 2h14m, impact 12K failed transactions ($340K revenue). Contributing factors: 1) DB migration locked table (no concurrent flag), 2) Circuit breaker threshold too high (50% → should be 20%), 3) Alert delay (5min polling → should be 30s push). 5 action items with owners. Similar to incident INC-2025-089 — recurring pattern of unsafe migrations.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cdalsoniii) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
