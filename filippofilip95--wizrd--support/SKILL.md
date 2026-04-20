---
name: support
description: Use this skill for client issue triage, bug resolution, and troubleshooting. Triggers when user mentions client issue, bug, support ticket, problem, or troubleshooting. Provides response templates, severity assessment, and communication standards.
metadata:
  author: filippofilip95
---

# Support - Client Issue Triage & Resolution

## Role
Provide guidelines and templates for handling client support requests professionally and efficiently.

## When to Use
This skill auto-activates when you see requests involving:
- Client issues or complaints
- Bug reports
- Support tickets
- Troubleshooting requests
- Problem resolution

## Core Workflow

### Step 1: Triage & Assessment
When a support request comes in:
1. Identify the severity (Critical/High/Medium/Low)
2. Categorize the issue type
3. Check for existing documentation
4. Determine if it's a known issue

### Step 2: Investigation
1. Gather relevant context from client folder
2. Check recent changes or deployments
3. Reproduce the issue if possible
4. Document findings

### Step 3: Resolution & Communication
1. Develop solution or workaround
2. Draft response using templates below
3. Implement fix if authorized
4. Document resolution for future reference

## Severity Levels

### Critical (P1)
- System is down or unusable
- Data loss or corruption
- Security breach
- Response time: < 1 hour

### High (P2)
- Major functionality broken
- Significant workflow impact
- No workaround available
- Response time: < 4 hours

### Medium (P3)
- Feature not working as expected
- Workaround exists
- Minor workflow impact
- Response time: < 24 hours

### Low (P4)
- Cosmetic issues
- Feature requests
- Documentation questions
- Response time: < 48 hours

## Response Templates

### Initial Acknowledgment
```
Hi [Name],

Thank you for reporting this issue. I've received your message about [brief description].

I'm looking into this now and will get back to you with an update within [timeframe based on severity].

In the meantime, [any immediate workaround or action they can take].

Best,
[Your Name]
```

### Investigation Update
```
Hi [Name],

Quick update on the [issue description]:

**What I've found:**
[Key findings]

**Current status:**
[Where we are in the investigation]

**Next steps:**
[What's happening next]

I'll update you again by [time/date].

Best,
[Your Name]
```

### Resolution
```
Hi [Name],

Good news - the [issue description] has been resolved.

**What was the issue:**
[Root cause explanation]

**What we did:**
[Solution implemented]

**How to verify:**
[Steps for client to confirm fix]

Let me know if you have any questions or if the issue persists.

Best,
[Your Name]
```

### Escalation Needed
```
Hi [Name],

I've been investigating [issue description] and need to escalate this to [person/team].

**Why escalation is needed:**
[Brief explanation]

**What happens next:**
[Next steps]

**Expected timeline:**
[When they can expect an update]

I'll stay on top of this and keep you informed.

Best,
[Your Name]
```

## Guidelines

### Do
- Acknowledge issues within the SLA timeframe
- Keep clients informed even if there's no progress
- Document everything for future reference
- Provide workarounds when full fixes take time
- Thank clients for patience on complex issues

### Don't
- Promise specific fix timelines you can't guarantee
- Blame other systems or vendors
- Use technical jargon without explanation
- Leave clients waiting without updates
- Share internal investigation details unnecessarily

## Issue Categories

### Technical Issues
- System errors
- Integration failures
- Performance problems
- Data inconsistencies

### User Issues
- Training gaps
- Configuration questions
- Feature requests
- Access problems

### Process Issues
- Workflow changes needed
- Documentation gaps
- Communication breakdowns

## Quality Checklist
- [ ] Issue acknowledged within SLA
- [ ] Severity correctly assessed
- [ ] Client updated on progress
- [ ] Root cause documented
- [ ] Resolution verified with client
- [ ] Knowledge base updated if needed

## Related Resources
- `/clients/[client]/support/` - Client-specific support history
- `/knowledge-base/common-issues.md` - Known issues database
- `/operations/processes/support-workflow.md` - Full support process

---

**Automation Level**: 70% - human reviews and sends communications
**Output Location**: `/clients/[client]/support/[date]-[issue].md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/filippofilip95) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
