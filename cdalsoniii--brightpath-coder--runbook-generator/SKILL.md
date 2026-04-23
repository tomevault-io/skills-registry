---
name: runbook-generator
description: Generate operational runbooks for alerts, deployments, and common procedures Use when this capability is needed.
metadata:
  author: cdalsoniii
---

# Runbook Generator Skill

Generate operational runbooks for alerts, deployments, and common procedures.

## Trigger Conditions
- New alert rule created
- New service deployed
- User invokes with "generate runbook" or "create runbook"

## Input Contract
- **Required:** Alert definition or service context
- **Required:** Expected failure mode or procedure
- **Optional:** Historical incident data, existing runbooks

## Output Contract
- Runbook document with step-by-step procedures
- Diagnostic commands and expected outputs
- Escalation contacts and criteria
- Verification steps

## Tool Permissions
- **Read:** Alert configs, service configs, monitoring dashboards, logs
- **Write:** Runbook documents in `docs/runbooks/`
- **Search:** Similar alerts and past incidents

## Execution Steps
1. Analyze the alert or service requiring a runbook
2. Identify likely failure modes and diagnostic steps
3. Write step-by-step mitigation procedures
4. Include diagnostic commands with expected outputs
5. Define escalation criteria and contacts
6. Add verification steps to confirm resolution
7. Link runbook to alert configuration

## Success Criteria
- Runbook covers the most common failure scenarios
- Each step has a concrete command or action
- Escalation path defined
- Runbook linked to its corresponding alert

## Escalation Rules
- Escalate if the runbook requires infrastructure access not available to on-call
- Escalate if the failure mode has no known mitigation

## Example Invocations

**Input:** "Generate a runbook for the 'payment-service-high-error-rate' alert"

**Output:** Runbook: 1) Check error logs (kubectl logs -l app=payment-service --since=5m), 2) Verify upstream dependencies (curl health endpoints), 3) Check recent deploys (gh run list --limit 5), 4) If deploy-related: rollback (kubectl rollout undo), 5) If dependency-related: check circuit breaker status, 6) Escalate to payments-team if unresolved in 15min.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cdalsoniii) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
