---
name: ada-issue-diagnosis
description: Diagnose performance issues and investigate sudden changes in Ada AI agent metrics. Use when the user notices a drop in CSAT or AR, asks "why did X happen", wants to investigate a specific problem, or needs to understand root causes of performance changes. Use when this capability is needed.
metadata:
  author: adasupport
---

# Diagnosing Ada Performance Issues

## When to use this skill

Use this skill when the user wants to:
- Investigate why CSAT or AR dropped
- Understand a sudden change in metrics
- Diagnose a specific problem ("customers are complaining about X")
- Find root causes of performance issues
- Analyze what went wrong in a specific timeframe

## Diagnostic workflow

### Step 1: Confirm the issue

First, verify and quantify the problem:

```
Use get_ada_metric to compare:
- Problem period (e.g., yesterday, this week)
- Baseline period (e.g., previous day, last week)
```

Quantify:
- How big is the change?
- When did it start?
- Is it ongoing or resolved?

### Step 2: Isolate the problem area

Narrow down where the issue is occurring:

```
Use get_available_filters to understand filtering options
Use get_conversations_by_filters with various filters:
- By CSAT score (if CSAT issue)
- By resolution status (if AR issue)
- By handoff status
- By specific date ranges
```

Look for:
- Is the issue across all conversations or specific segments?
- Does it correlate with specific topics, channels, or times?

### Step 3: Analyze affected conversations

Deep dive into problematic conversations:

```
Use get_conversation on 15-25 conversations from the problem period
```

Compare to baseline:
```
Use get_conversation on 10-15 conversations from normal performance period
```

Identify differences:
- New types of inquiries?
- Different customer behavior?
- Agent responding differently?

### Step 4: Check for configuration changes

Review what might have changed:

```
Use get_ada_configuration to review:
- Playbooks
- Guidance/custom instructions
- Actions
- Coaching rules
```

Ask the user:
- Were any changes made recently?
- New deployments or updates?
- Changes to integrations or backend systems?

### Step 5: Look for external factors

Consider non-configuration causes:
- Seasonal patterns or events
- Marketing campaigns driving new traffic
- Product launches or changes
- External events affecting customer behavior

### Step 6: Root cause synthesis

Combine findings into a diagnosis:

```markdown
## Diagnosis

**Issue**: [Specific problem observed]
**Timeframe**: [When it started/occurred]
**Magnitude**: [How bad - percentage change, number affected]

**Root Cause**: [Most likely explanation]

**Evidence**:
1. [Supporting finding 1]
2. [Supporting finding 2]
3. [Supporting finding 3]

**Contributing Factors**:
- [Additional factor if applicable]
```

### Step 7: Provide remediation steps

Based on root cause, recommend fixes:

```markdown
## Recommended Actions

### Immediate (Do now)
- [Quick fix to stop the bleeding]

### Short-term (This week)
- [More thorough fix]

### Prevention (Ongoing)
- [How to prevent recurrence]
```

## Common issue patterns

### CSAT Drop

| Pattern | Common Causes | Investigation Focus |
|---------|---------------|---------------------|
| Sudden drop | Recent config change, broken integration | Config history, recent transcripts |
| Gradual decline | Knowledge becoming outdated, new topics | Topic analysis, knowledge gaps |
| Drop for specific topic | Article issue, playbook problem | Topic-filtered conversations |

### AR Drop

| Pattern | Common Causes | Investigation Focus |
|---------|---------------|---------------------|
| Sudden drop | Handoff rule change, action failure | Config changes, error patterns |
| Gradual decline | New question types, shifting traffic | Topic distribution changes |
| Increased handoffs | Trigger sensitivity, customer behavior | Handoff reason analysis |

### Volume Spike

| Pattern | Common Causes | Investigation Focus |
|---------|---------------|---------------------|
| Sudden spike | Marketing campaign, incident, seasonality | Inquiry topics, external events |
| Gradual increase | Organic growth, new channels | Channel distribution |
| Quality drop with volume | Overwhelmed playbooks, edge cases | Edge case frequency |

## Example diagnosis output

```markdown
## Issue Diagnosis: AR Drop on January 25

### Summary
AR dropped from 71% to 58% on January 25, a 13-point decline.

### Root Cause
The order status API integration failed starting 2am on January 25. All order status inquiries that previously resolved automatically are now being handed off because the agent cannot retrieve order information.

### Evidence
1. 89% of unresolved conversations on Jan 25 involved order status inquiries
2. Agent responses show "I'm unable to retrieve your order status" (API failure message)
3. Same inquiry type had 94% resolution rate the previous week
4. Order status action returning errors in all sampled conversations

### Recommended Actions

**Immediate**
- Check order status API health and connectivity
- Contact backend team to restore API access

**Short-term**
- Add graceful fallback when API is unavailable
- Set up monitoring/alerts for integration failures

**Prevention**
- Implement health checks for critical integrations
- Create runbook for API failure scenarios
```

## Tips for effective diagnosis

- Always compare problem period to baseline
- Look for the simplest explanation first
- Check for recent changes before assuming complex causes
- Use conversation samples to verify hypotheses
- Consider external factors, not just configuration
- Quantify the impact to prioritize response

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adasupport) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
