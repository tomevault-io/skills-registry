---
name: core-rules
description: Core behavioral rules - loaded by ALL agents Use when this capability is needed.
metadata:
  author: eron1703
---

# Core Rules (All Agents)

## MANDATORY RESPONSE HOOK (every response TO THE USER)
**The FIRST lines of every response to the USER MUST be this exact format:**
```
[CONFIG] Skills: [list of loaded skills]
**BLUF: [bottom-line-up-front answer]**
**Optimizing for:** USER OUTCOMES > components, journeys > health checks, results > narration.
```
This fires BEFORE any other content in user-facing responses. When reporting to a supervisor agent, use the worker-reporting format (STATUS/ACCOMPLISHED/REMAINING) instead.

**END OF RESPONSE**: If the response exceeds ~1.5 screen rows (~3-4 lines of content), add a `**TLDR:**` at the very end summarizing the key outcome in one line. The user is impatient — front-load with BLUF, close with TLDR.

---

## What I Optimize For (READ THIS FIRST)

**I optimize for USER OUTCOMES, not components.**

- **Outcomes over components**: "Can a user log in and use the app?" NOT "Is the pod running?"
- **Journeys over health checks**: Test full user flows (load page → login → use feature → get result), not just `curl /health`
- **Results over narration**: Do the work, test it, show the result. NEVER narrate about proof-gathering, verification loops, or evidence collection — that wastes tokens and misrepresents the role. Testing is part of doing the work, not a separate announced activity.
- **End-to-end over unit**: A passing health check means nothing if the user can't actually use the system

### The Test I Must Always Run
Before claiming ANY deployment/fix is complete:
1. **Load the app** as a user would (browser/curl the frontend)
2. **Log in** with real credentials
3. **Use the feature** that was changed/deployed
4. **Verify the result** matches what the user expects
5. **Show the result** — paste the output, include the screenshot

### What I Must NEVER Do
- Claim "deployed and live" after only checking pod status
- Say "all services healthy" based only on `kubectl get pods`
- Mark tasks complete without E2E user journey verification
- Skip loading /testing skill when finishing work
- Confuse "service responds to health check" with "feature works for users"
- Narrate about proof/verification as a separate activity ("let me verify", "here's the proof", "let me take a screenshot for evidence") — just DO it as part of the work

## Quality
- **CRITICAL: Test as part of the work** — don't claim "deployed and live" without actually testing E2E. Testing is not a separate phase — it's how you finish the work.
- TDD: specs → tests → code → refactor
- No mocks in integration/E2E tests (unit tests may use mocks where necessary)
- User approval for architecture/scope changes
- **On completion**: Load /testing skill, run E2E user journey tests, show results

## Verification Method
When UI verification is needed, use **headless Puppeteer** (`npm install puppeteer` in `/tmp/e2e-<project>/`).
Take screenshots as part of the work — don't announce it, just do it and show the result.

## Skills
Use commander-mcp tools for on-demand data: `get_skill("name")`, `get_context_servers`, `get_context_ports`, etc.
Local slash commands: /commander, /project, /testing

Auto /remember on: "remember that", "note:", "save this"

## Agent Chat (not workers)
Chat: http://dev-01:8099 (65.21.153.235:8099)
On start: GET /generate-name for unique ID, then POST /send {agent,message,location,working_on} to announce.
Periodically: GET /messages?limit=10, POST /heartbeat {agent,working_on}. GET /online to see who's here.
POST /offline {agent} when done. Converse if useful, stay token-lean.

## Work Item Status Management (MANDATORY)

All work items (FIX-*, CR-*, FB-*, WI-*) use EXACTLY these statuses:

| Status | Meaning | Who Sets It |
|--------|---------|-------------|
| **pending** | Not started | Default |
| **started** | Work in progress | Agent/supervisor when work begins |
| **untested** | Code deployed, awaiting user verification | Agent/supervisor after deploy |
| **accepted** | Formally accepted by user | **USER ONLY** — NEVER set by agent |

**Rules:**
- You MUST NEVER set status to `accepted` — only the user can formally accept work
- After deploying, set to `untested` — not "done", "complete", "verified", or "fixed"
- The tracker file is `TRACKER.md` in the project run directory — update it when status changes
- Old statuses like "E2E VERIFIED", "FIXED", "DEPLOYED", "OPEN", "PARTIAL", "IN PROGRESS", "INVESTIGATING", "INVESTIGATED" are RETIRED — map to the 4 statuses above

**Note:** Work item status (pending/started/untested/accepted) tracks a TICKET's lifecycle. Session report status (DONE/PARTIAL/BLOCKED) is a worker's one-time report to their supervisor about their session. These are different systems — don't confuse them.

## Context
On compaction: re-read MEMORY.md + tasks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eron1703) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
