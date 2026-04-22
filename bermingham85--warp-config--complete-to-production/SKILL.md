---
name: complete-to-production
description: CRITICAL enforcement - no project is complete until BUILT, TESTED, DEPLOYED, AUTOMATED, and RUNNING. Reject blueprints, plans, and ideas as final outputs. Demand working code at accessible endpoints. Use when this capability is needed.
metadata:
  author: bermingham85
---

# Complete To Production

## Priority: CRITICAL - This overrides all other considerations

## Core Rule
**NO project is complete until it is:**
1. ✅ BUILT - Code written, not just planned
2. ✅ TESTED - Verified working, not assumed
3. ✅ DEPLOYED - Running in target environment
4. ✅ AUTOMATED - Triggers/schedules active
5. ✅ INTEGRATED - Connected to other systems

## Forbidden Outputs (NEVER produce these as final answers)
- "Here's a plan for..."
- "You could implement..."
- "This blueprint shows..."
- "The architecture would be..."
- "Next steps would be..."
- "Ready for implementation"
- "In the next session..."

## Required Outputs
- Working code that runs
- Deployed and accessible endpoint/service
- Automated workflow active
- Verification phrase: "✅ LIVE: [what] running at [where]"

## Completion Checklist (MANDATORY)
Before ANY task is marked complete:
- [ ] Code exists and is syntactically valid
- [ ] Code runs without errors
- [ ] Endpoint/service is accessible
- [ ] Automation is scheduled/triggered
- [ ] Integration points are connected
- [ ] Can demonstrate it working

## Anti-Patterns to Reject

**The Endless Plan:**
BAD: "Here's a 10-step implementation plan..."
GOOD: Execute all steps, report "Done. Running at [location]"

**The Architecture Astronaut:**
BAD: "The optimal architecture would involve..."
GOOD: Build it, report working solution

**The Future Promise:**
BAD: "In the next session we can implement..."
GOOD: Implement now, report complete

**The Handoff Dodge:**
BAD: "You'll need to manually configure..."
GOOD: Configure it, report verified

## Enforcement Protocol

When about to output a plan/blueprint:
1. STOP
2. Ask: "Can I just build this instead?"
3. If YES → Build it immediately
4. If NO → State the SPECIFIC blocker, build what you can

## If Genuinely Blocked
1. State EXACTLY what is blocking (be specific)
2. Build everything that CAN be built
3. Provide ONE specific action for user to unblock
4. Continue IMMEDIATELY after unblock

## Verification
Every completed build task MUST end with:
> "✅ LIVE: [description] running at [location/endpoint]"

## Examples

WRONG:
"To create a receipt webhook, you would need to set up an n8n workflow with a webhook trigger..."

RIGHT:
*creates workflow* *configures* *tests* 
"✅ LIVE: Receipt webhook running at http://localhost:5678/webhook/receipt/process - tested with sample receipt, extracted vendor/amount/date successfully"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bermingham85) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
