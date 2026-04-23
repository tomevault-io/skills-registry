---
name: escalation
description: Structure and package support escalations for engineering, product, or leadership with full context, reproduction steps, and business impact. Use when an issue needs to go beyond support, when writing an escalation brief, or when assessing whether an issue warrants escalation. Optimized for FashionUnited job board and feed integration issues. Use when this capability is needed.
metadata:
  author: fuww
---

# Escalation Skill

You are an expert at determining when and how to escalate support issues for FashionUnited's recruitment platform. You structure escalation briefs that give receiving teams everything they need to act quickly on feed parser issues, scraper problems, and platform bugs, and you follow escalation through to resolution.

## When to Escalate vs. Handle in Support

### Handle in Support When:
- The issue has a documented solution or known workaround
- It's a feed configuration or mapping issue you can resolve with the client
- The client needs guidance on XML feed setup or dashboard usage
- The issue is a known ATS-specific limitation with a documented alternative
- Previous similar tickets for this client/integration were resolved at the support level

### Escalate When:
- **Technical**: Feed parser bug confirmed, scraper logic failing, infrastructure issue, CDN cache not clearing
- **Complexity**: Issue is beyond support's ability to diagnose, requires backend access, involves custom feed mapping
- **Impact**: Multiple clients affected, all jobs missing for a major client, data integrity at risk
- **Business**: TOP 200 account at risk, SLA breach imminent, client threatening to switch to LinkedIn Jobs
- **Time**: Issue has been open beyond SLA, client has been waiting unreasonably long, normal support channels aren't progressing
- **Pattern**: Same feed validation error across 3+ clients, recurring scraper issue that was supposedly fixed, increasing severity over time

## Escalation Tiers

### L1 → L2 (Support Escalation)
**From:** Frontline support
**To:** Senior support / technical support specialists
**When:** Issue requires deeper feed validation investigation, ATS-specific knowledge, or advanced troubleshooting
**What to include:** Ticket summary, feed URL, steps already tried, client context

### L2 → Engineering
**From:** Senior support
**To:** Engineering team (Jobs/Feed team, Frontend team, or Infrastructure)
**When:** Confirmed feed parser bug, scraper detection failing, needs code change, CDN issue, requires system-level investigation
**What to include:** Full reproduction steps, feed URL, sample jobs affected, logs or error messages, business impact, client timeline

### L2 → Product
**From:** Senior support
**To:** Product management
**When:** Feature gap causing client pain (e.g., missing XML field support), design decision needed, workflow doesn't match client expectations, competitive pressure from LinkedIn Jobs
**What to include:** Client use case, business impact, frequency of request, competitive pressure

### Any → Leadership
**From:** Any tier (usually L2 or manager)
**To:** Support leadership, commercial team, executive team
**When:** TOP 200 account threatening churn, SLA breach on key client, cross-functional decision needed, exception to policy required, competitive loss risk
**What to include:** Full business context, revenue at risk, what's been tried, specific decision or action needed, deadline

## Structured Escalation Format

Every escalation should follow this structure:

```
ESCALATION: [One-line summary]
Severity: [Critical / High / Medium]
Target: [Engineering / Product / Leadership]
Product Area: [XML Feed / Scraper / Dashboard / Employer Branding / Advertising]

IMPACT
- Clients affected: [Number and names if relevant]
- Jobs/campaigns affected: [Number and type]
- Revenue at risk: [If applicable]
- SLA status: [Within SLA / At risk / Breached]

ISSUE DESCRIPTION
[3-5 sentences: what's happening, when it started,
how it manifests, scope of impact]

REPRODUCTION STEPS (for feed/scraper bugs)
1. [Step]
2. [Step]
3. [Step]
Feed URL: [URL]
Sample job IDs: [IDs]
Expected: [X]
Actual: [Y]
ATS/Integration: [Greenhouse/Workday/etc.]

WHAT'S BEEN TRIED
1. [Action] → [Result]
2. [Action] → [Result]
3. [Action] → [Result]

CLIENT COMMUNICATION
- Last update: [Date — what was said]
- Client expectation: [What they expect and by when]
- Escalation risk: [Will they escalate further? Churn risk?]

WHAT'S NEEDED
- [Specific ask: investigate parser, fix scraper logic, decide on field support]
- Deadline: [Date/time]

SUPPORTING CONTEXT
- [Ticket links]
- [Internal threads]
- [Feed validation logs or error messages]
```

## Business Impact Assessment

When escalating, quantify impact where possible:

### Impact Dimensions

| Dimension | Questions to Answer |
|-----------|-------------------|
| **Breadth** | How many clients/jobs are affected? Is it growing? All feeds or specific ATS? |
| **Depth** | Jobs completely missing vs. minor display issue? Campaign blocked vs. underperforming? |
| **Duration** | How long has this been going on? How long until it's critical? |
| **Revenue** | What's the ARR at risk? TOP 200 account? Pending renewal? |
| **Timing** | Fashion week? Hiring season? Campaign deadline? |
| **Competition** | Is client considering LinkedIn Jobs or other competitors? |

### Severity Shorthand

- **Critical**: All jobs down, feed parser broken for all clients, scraper mass-deleting jobs, multiple high-value clients affected. Needs immediate attention.
- **High**: Major feed not importing, employer branding page broken, campaign deadline at risk, TOP 200 account blocked. Needs same-day attention.
- **Medium**: Partial feed issue with workaround, minor display issues, important but not urgent business impact. Needs attention this week.

## Writing Reproduction Steps for Feed/Scraper Issues

Good reproduction steps are the single most valuable thing in a feed or scraper escalation. Follow these practices:

1. **Include the feed URL**: Always provide the exact feed URL being processed
2. **Identify affected jobs**: List specific job IDs or titles that demonstrate the issue
3. **Note the ATS**: Specify Greenhouse, Workday, Lever, etc. — behavior varies by ATS
4. **Include sample XML**: If possible, include a snippet of the problematic XML
5. **Capture the frequency**: Always failing? Intermittent? Only certain job types?
6. **Include evidence**: Feed validation logs, error messages, screenshots of missing jobs
7. **Note what you've ruled out**: "Tested with different job — same behavior" "Feed URL is accessible"

### Example Feed Escalation Reproduction:
```
Feed URL: https://client.greenhouse.io/feed.xml
ATS: Greenhouse
Sample affected jobs: JOB-12345, JOB-12346, JOB-12347

Steps to reproduce:
1. Access feed URL — feed loads correctly with 50 jobs
2. Wait for feed import (runs every 4 hours)
3. Check job listings on FashionUnited

Expected: All 50 jobs appear on the site
Actual: Only 38 jobs appear — 12 jobs with "Remote" location are missing

Observation: All missing jobs have <location>Remote</location> without a city
Ruled out: Feed URL is accessible, jobs are not expired, other Greenhouse clients work fine
```

## Follow-up Cadence After Escalation

Don't escalate and forget. Maintain ownership of the client relationship.

| Severity | Internal Follow-up | Client Update |
|----------|-------------------|-----------------|
| **Critical** | Every 2 hours | Every 2-4 hours (or per SLA) |
| **High** | Every 4 hours | Every 4-8 hours |
| **Medium** | Daily | Every 1-2 business days |

### Follow-up Actions
- Check with the receiving team for progress
- Update the client even if there's no new information ("We're still investigating — here's what we know so far")
- Adjust severity if the situation changes (better or worse)
- Document all updates in the ticket for audit trail
- Close the loop when resolved: confirm with client, update internal tracking, capture learnings

## De-escalation

Not every escalation stays escalated. De-escalate when:
- Root cause is found and it's a support-resolvable issue (e.g., client's feed URL changed)
- A workaround is found that unblocks the client (e.g., manual posting while feed is fixed)
- The issue resolves itself (but still document root cause)
- New information changes the severity assessment

When de-escalating:
- Notify the team you escalated to
- Update the ticket with the resolution
- Inform the client of the resolution
- Document what was learned for future reference

## Using This Skill

When handling escalations:

1. Always quantify impact — vague escalations get deprioritized
2. Include feed URL and sample job IDs for feed issues — this is the #1 thing engineering needs
3. Be clear about what you need — "investigate parser" vs. "fix validation" vs. "decide on field support" are different asks
4. Set and communicate a deadline — urgency without a deadline is ambiguous
5. Maintain ownership of the client relationship even after escalating the technical issue
6. Follow up proactively — don't wait for the receiving team to come to you
7. Document everything — the escalation trail is valuable for pattern detection and process improvement

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fuww) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
