---
name: dev-loop
description: Standard development SOP for ALL code changes — bug fixes, features, refactors, any implementation. Defines the universal pipeline (investigate → fix → test → deploy → QA) and scope-based decision matrix. Also provides continuous scanning mode for autonomous operation. Use when implementing ANY code change, fixing bugs, building features, or when user says "start dev loop", "cowork", "fix this", "implement this", "build this". Each workspace can override with local skill. Use when this capability is needed.
metadata:
  author: artymclabin
---

# Dev Loop — Universal Development SOP

> **Part of:** Autonomous Issue Dispatch System
> See `~/.claude/skills/autonomous-issue-dispatch/SKILL.md` for full architecture.

## 🚨 This Skill Applies to ALL Development Work

This is NOT just for continuous scanning. **Every code change** — whether from a direct user request, a GitHub issue, a Slack bug report, or autonomous scanning — follows this SOP. The only variable is **scope**, which determines which steps are mandatory.

### Scope: What This SOP Covers

**IN SCOPE (dev-loop applies):**
- Project/application code that lives in a repository
- Code that will be maintained, deployed, used by others
- Bug fixes, features, refactors to existing codebase

**OUT OF SCOPE (dev-loop does NOT apply):**
- Temporary scripts Claude writes for one-off automation (sync scripts, data transforms, scratchpad utilities)
- Context engineering files (CLAUDE.md, skills, agents) — these have their own governance
- Documentation-only changes (.md, .txt)

**Signal words for exemption:** When writing a temporary script, mention "temporary script", "one-off", or "scratchpad" in the conversation — the stop hook recognizes these as exempt from dev-loop enforcement.

---

## Development Pipeline (Universal)

```
ANY code change request
  │
  ▼
┌─── SCOPE ASSESSMENT ──────────────────────────────────────┐
│                                                             │
│  How many files? What's the blast radius?                   │
│                                                             │
│  SMALL (1-3 files, clear fix, no architecture decisions)    │
│  → Investigate → Fix → Test → Deploy → QA                  │
│                                                             │
│  MEDIUM (4+ files, crosses modules, needs planning)         │
│  → Investigate → Plan → Fix → Test → Deploy → QA           │
│                                                             │
│  LARGE (new system, schema change, architecture)            │
│  → CTO Agent orchestrates full pipeline                     │
│  → Size alone is NEVER a reason to defer or skip            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Step-by-Step Pipeline

| # | Step | Small | Medium | Large | Details |
|---|------|-------|--------|-------|---------|
| 1 | **Investigate** | ✅ | ✅ | ✅ | Understand the problem. Read code, reproduce, root cause. |
| 2 | **Plan** | Skip | ✅ | ✅ | Use EnterPlanMode or CTO Agent. Get user approval. |
| 3 | **Fix** | ✅ | ✅ | ✅ | Implement the change. |
| 4 | **Test** | ✅ | ✅ | ✅ | Run test suite. Add/update tests if touching testable logic. |
| 5 | **Commit** | ✅ | ✅ | ✅ | Commit with descriptive message. |
| 6 | **Deploy** | ✅ | ✅ | ✅ | Per repo's deploy mode (auto-prod, auto-stage, approval-required). |
| 7 | **QA Submit** | ✅ | ✅ | ✅ | **If project has `.claude/skills/qa-submission/SKILL.md`:** invoke it. Do NOT post to Slack directly. **If no skill exists:** skip this step silently (not all repos have a QA workflow). |
| 8 | **Report** | ✅ | ✅ | ✅ | Tell user: what changed, deployed (yes/no), QA status. |

**Steps 4-7 are MANDATORY for every code change.** The Stop hook enforces this.


### 🚨 No Debug Code to Production

**Never deploy diagnostic/debug code to production.** This includes:
- Visible UI indicators (`[debug]`, `[no source]`, red text markers)
- Temporary state dumps rendered in the UI
- "Is this working?" test elements

**Debug locally first.** If you need runtime diagnostics:
- Use `console.log()` (invisible to users, visible in DevTools)
- Use local dev server (`npm run dev`)
- Ask user to check browser console

**Why:** Deploying debug code to production treats prod as staging. Even "harmless" indicators violate user trust and professional standards.

### CTO Agent — What It Is and How to Invoke

**The CTO Agent is `strategic-cto-planner`** — a specialized orchestrator that coordinates implementation work.

**To invoke the CTO Agent:**
```
Task tool with subagent_type: "strategic-cto-planner"
```

**What CTO Agent does:**
- Delegates ALL coding to `developer` agent (never writes code itself)
- Coordinates: Developer → QA Engineer → Integration Tester
- Enforces TDD (tests written AND executed)
- Validates completion gate before declaring done
- Maintains objectivity by not implementing

**CTO Agent orchestration flow:**
```
You → CTO Agent (strategic-cto-planner)
         ↓
    Developer agent (writes code + tests)
         ↓
    QA Engineer agent (reviews + validates)
         ↓
    Integration Tester agent (E2E validation)
         ↓
    Completion Gate (all criteria verified)
```

**🚨 NEVER dispatch directly to `developer` agent for Continuous Scan Mode.** Always go through CTO. The separation prevents confirmation bias where implementers evaluate their own work.

**When CTO Agent is required:**
- Continuous Scan Mode (bug-intake, dev-loop scanning)
- Medium/Large scope work (4+ files, architecture decisions)
- Any work that needs orchestrated validation

**When CTO Agent is optional:**
- Ad-Hoc Mode with small scope (see below)

### When User Says "Don't Bother with QA" or "Just a Quick Fix"

Follow the pipeline anyway. The user's CLAUDE.md says "EVERY change gets submitted to QA. No exceptions based on perceived simplicity." If the user explicitly overrides in the moment, comply but note it.

### QA Submission — Skill, Not Raw API

The qa-submission skill encapsulates the full QA workflow (GitHub issue, Slack formatting, labels, poka-yoke). Posting directly to a QA channel via Slack API bypasses all of this.

**Correct:** Invoke `.claude/skills/qa-submission/SKILL.md` (per-project)
**Wrong:** Call Slack MCP tools directly to post a QA message
**Wrong:** Skip QA because "it's a small fix"

If the current project has no qa-submission skill, skip QA submission silently. Not all repos have a QA workflow — this is expected, not a gap.

### 🚨 QA Responses Live in Slack, NEVER on GitHub

QA operators (QA_PERSON, QA_PERSON_B, etc.) verify fixes and report pass/fail **exclusively in Slack** (the QA channel thread where the submission was posted). They do NOT comment on GitHub issues.

**When checking QA status:**
- ✅ Scan Slack QA channel threads for operator responses
- ❌ NEVER look for QA operator comments on GitHub issues — they won't be there

**When assessing the QA pipeline health:**
- `pending-qa` label on GitHub = submitted, awaiting Slack verification
- Absence of GitHub comments from QA operators is NORMAL, not a pipeline failure
- To check if QA passed: read the Slack thread, not the GitHub issue

**Anti-pattern (real incident, Mar 2026):** A session surveyed 186 `pending-qa` GitHub issues, found zero operator comments, and concluded "the entire QA pipeline is stuck." In reality, the QA operator was verifying ~15 items/day in Slack. The session looked in the wrong place and drew a false conclusion.

---

## Ad-Hoc Mode (Direct User Request)

**🚨 STRICT RULE: Ad-Hoc Mode ONLY applies when ALL conditions are met:**

1. User gives a **specific, single task** directly in conversation
2. User does **NOT** invoke any skill (bug-intake, dev-loop, dispatch, etc.)
3. User does **NOT** say "scan", "intake", "check bugs", "run the loop", "cover all", etc.

**If user invokes ANY skill or mentions scanning/batch work:**
→ That's **Continuous Scan Mode** → Use full pipeline with CTO Agent (see below)

| User says... | Mode | Why |
|-------------|------|-----|
| "Fix this specific bug in file X" | Ad-Hoc ✅ | Single, specific task |
| "Change button color to blue" | Ad-Hoc ✅ | Single, specific task |
| "Run bug intake" | **Continuous Scan** ❌ | Invokes skill |
| "Scan Slack for bugs" | **Continuous Scan** ❌ | "Scan" = batch |
| "Fix all the bugs" / "Cover all gaps" | **Continuous Scan** ❌ | Batch = scan |

---

When Ad-Hoc Mode applies:

```
User request → Investigate → Fix → Test → Commit → Deploy → QA → Report
```

**No intake scan. No dispatcher triage. No iteration loop.** Just the pipeline.

**Decision matrix for direct requests:**

| User says... | Scope | Action |
|-------------|-------|--------|
| "Fix this bug" / "This is broken" | Usually small | Pipeline steps 1,3-8 |
| "Build this feature" / "Implement this" | Usually medium | Pipeline steps 1-8 |
| "Refactor X" / "Redesign Y" | Usually large | CTO Agent or defer |
| "Just change this text" / "Rename X" | Small | Pipeline steps 3-8 (investigate optional) |

---

## Continuous Scan Mode

**Trigger:** "Start dev loop" / "Run continuous scan" / "Scan and fix bugs" / "Cowork with QA_PERSON"

Uses `~/.claude/skills/bug-intake/SKILL.md` for each scan iteration.

```
START (silently - no announcement message)
  │
  ▼
┌─── ITERATION ──────────────────────────────────────────────┐
│                                                             │
│  PHASE 0: UNCOMMITTED HOUSEKEEPING                          │
│  Previous sessions sometimes leave uncommitted changes      │
│  behind (context pressure, crashes, forgot to commit).      │
│  These orphans block future dispatches ("WIP conflict")     │
│  and accumulate until someone cleans them up.               │
│                                                             │
│  Procedure:                                                 │
│    1. `git status --short` — any modified tracked files?    │
│    2. If none → skip to Phase 1                             │
│    3. For each modified file, check mtime:                  │
│       `stat -c %Y <file>` (Unix/Git Bash on Windows)       │
│       age_hours = (now - mtime) / 3600                      │
│    4. If ALL modified files are < 5 hours old → skip        │
│       (likely an active parallel session — don't touch)     │
│    5. If ANY modified file is >= 5 hours old:               │
│       a. `git diff` — review the changes                    │
│       b. Assess: coherent fix/refactor? SSoT cleanup?       │
│       c. Run tests (npm test or equivalent)                 │
│       d. If tests pass → commit with descriptive message    │
│       e. If tests fail → fix the test gap, then commit      │
│       f. Push if repo deploys on push (auto-prod/stage)     │
│    6. Log: "Housekeeping: committed N stale files (Xh old)" │
│                                                             │
│  Why 5 hours: Owner + parallel dev loops commit within ~2h. │
│  5h is a safe upper bound — older = definitely orphaned.    │
│                                                             │
│  Anti-pattern (real incident, Mar 2026):                    │
│  6 files left uncommitted by a previous session. Next       │
│  session labeled them "user WIP" and refused to touch them. │
│  4 of 8 actionable issues were skipped as "WIP conflict"    │
│  when they could have been dispatched after a simple commit.│
│                                                             │
│  PHASE 1: INTAKE SCAN                                       │
│  Run bug-intake skill (full scan of configured channels)    │
│  Intake sources: Slack channels, Google Sheets (per-repo)   │
│  Also checks: GitHub issues (existing, not from intake)     │
│  Identifies: new bugs, verification responses, questions    │
│                                                             │
│  PHASE 2: TRIAGE (Dispatcher)                               │
│  Run issue-dispatcher for the repo's GitHub issue queue:    │
│    - Hygiene checks (labels, staleness, duplicates)         │
│    - Categorize: Actionable / Blocked / Backlog / Needs QA  │
│    - Prioritize: severity > effort > dependencies > impact   │
│      Age is minor tiebreaker: newer first (more relevant)   │
│    - Issues from Phase 1 AND pre-existing GitHub issues     │
│      are triaged together — one unified queue               │
│                                                             │
│  🚨 CANONICAL ISSUE QUERY — use this exact command:        │
│    gh issue list --state open --json number,title,labels    │
│      --limit 50                                             │
│  NEVER use pre-filtered queries (--label, --search) as     │
│  the primary scan — they miss issues. Filter AFTER fetch.   │
│  After filtering out blocked/needs-info/pending-qa, sanity  │
│  check: if < 3 issues remain on a repo with 50+ open       │
│  issues, re-run with --limit 100 or broader filters.       │
│                                                             │
│  PHASE 3: PROCESS ACTIONABLE ISSUES                         │
│  For each actionable issue (in priority order):             │
│                                                             │
│  🚨 CONTEXT BUDGET RULE — see "Investigation Depth" below  │
│  The loop is a DISPATCHER, not an investigator.             │
│  Deep codebase investigation belongs to the dispatched      │
│  CTO/developer agent, not the loop orchestrator.            │
│                                                             │
│  Actionable issue →                                         │
│    1. SHALLOW TRIAGE ONLY (loop orchestrator):              │
│       a. Read issue body + comments (already loaded)        │
│       b. Classify: small bug vs medium/large feature        │
│       c. If genuinely ambiguous spec: label needs-info,     │
│          move to Blocked, continue to next issue            │
│    2. SMALL BUG (1-3 files, clear root cause):              │
│       → Fix directly in the loop                            │
│       → Investigate → Fix → Test → Deploy → QA              │
│    3. ANYTHING ELSE (features, 4+ files, cross-module):     │
│       → Dispatch to CTO Agent IMMEDIATELY                   │
│       → Pass issue body + spec as prompt context            │
│       → Do NOT read codebase files first                    │
│       → CTO agent investigates in its own context           │
│    4. CTO orchestrates: Developer, QA Engineer,             │
│       Integration Tester, Completion Gate                   │
│    5. TDD — all tests must pass before deploy               │
│    6. Deploy per repo's deploy mode                         │
│    7. Communicate result (per bug-intake Step 7)            │
│    8. Submit for QA (per qa-submission, if applicable)      │
│    9. Log: "Fixed #N, deployed/awaiting approval"           │
│                                                             │
│  Big work (flagged by Dispatcher) →                         │
│    FIRST: Re-read the full issue body. Classify:            │
│    a) Clear spec (WHAT is defined) → Dispatch via CTO agent │
│       Big scope with clear requirements is NOT blocked.     │
│    b) Unclear spec (genuinely ambiguous) → Defer to owner   │
│       1. If from Slack: Add :hourglass: reaction            │
│       2. Create/update GitHub issue                         │
│       3. Reply: "Needs owner clarification."                │
│       4. Log: "Deferred #N (needs product decision)"        │
│    ⚠️ "Big" ≠ "blocked". Only defer when WHAT is unclear.  │
│                                                             │
│  🚨 PARALLEL DISPATCH — maximize throughput:                │
│    When multiple issues are ready, dispatch them in         │
│    PARALLEL via worktree-isolated CTO agents. Don't         │
│    serialize: "dispatch #1, wait, dispatch #2." Instead:    │
│    "dispatch #1 + #2 + #3 simultaneously in worktrees."    │
│    Merge results sequentially after agents complete.         │
│    The only constraint is file conflict between issues —    │
│    if two issues touch the same files, serialize those two. │
│    Context budget is NOT a reason to serialize — worktree   │
│    agents have their own context windows.                   │
│                                                             │
│  🚨 SIZE ≠ SKIP — the dispatch decision:                   │
│    The ONLY reason to skip/defer an issue is genuinely      │
│    unclear spec. NOT: "it's big", "it's lower priority",    │
│    "it's aspirational", "it's complex."                     │
│    Big + clear spec = dispatch. Small + unclear = defer.    │
│    Priority ordering decides SEQUENCE, not WHETHER to do it.│
│    If you have capacity (context, time), dispatch ALL       │
│    clear-spec issues, not just the top-priority one.        │
│                                                             │
│  Anti-pattern (real incident, Mar 2026):                    │
│  Session dispatched 1 issue, then listed 6 others as "not  │
│  dispatched" with reasons like "lower priority" and "very   │
│  large, aspirational." All 6 had clear specs. 3 had zero   │
│  file conflicts. They could have been dispatched in         │
│  parallel. Doing nothing is never better than doing         │
│  something when the spec is clear.                          │
│                                                             │
│  🚨 MANDATORY ISSUE DISPOSITION TABLE                       │
│  Every Continuous Scan iteration MUST produce a disposition │
│  table accounting for ALL triaged issues:                   │
│                                                             │
│  | # | Issue | Disposition    | Evidence              |    │
│  |---|-------|----------------|-----------------------|    │
│  | 1 | #400  | Fixed directly | Commit abc123         |    │
│  | 2 | #382  | Dispatched     | Agent ID xyz          |    │
│  | 3 | #320  | Deferred       | "No acceptance criteria"|  │
│                                                             │
│  Valid dispositions:                                        │
│    fixed, dispatched, deferred (reason), closed (reason),  │
│    already-implemented, blocked (reason)                    │
│  Invalid dispositions (stop hook flags these):             │
│    "too large", "lower priority", "aspirational",          │
│    "complex", "not dispatched"                             │
│                                                             │
│  If an issue has a clear spec, "deferred" requires a       │
│  reason that is NOT about size/complexity.                  │
│  Missing table = stop hook violation.                       │
│                                                             │
│  Verification response (reporter confirms) →                │
│    1. Close GitHub issue                                    │
│    2. Replace :eyes: with :white_check_mark:                │
│    3. Log: "Closed #N (verified)"                           │
│                                                             │
│  Verification response (reporter/QA rejects) →              │
│    1. Read rejection details                                │
│    2. Route back to CTO Agent (skip Handler — diagnosis     │
│       exists, just needs iteration)                         │
│    3. CTO re-fixes, tests, deploys                          │
│    4. Re-submit for QA                                      │
│    5. Update thread with new fix details                    │
│    6. Log: "Re-fixed #N after rejection"                    │
│                                                             │
│  Direct question from team member →                         │
│    1. Read question context                                 │
│    2. Reply in thread with answer                           │
│    3. If question requires owner decision, say so           │
│    4. Log: "Answered question in thread"                    │
│                                                             │
│  Big work owner response (in :hourglass: thread) →          │
│    1. Read owner's response                                 │
│    2. Add :eyes: to the response (mark as processed)        │
│    3. If approved: remove :hourglass:, route to CTO Agent   │
│    4. If answered questions: update GitHub issue with        │
│       answers, re-evaluate scope (may become actionable)    │
│    5. If "not now" / "backlog": log, keep deferred          │
│    6. Log: "Owner responded to #N: [approved/answered/      │
│       deferred]"                                            │
│                                                             │
│  PHASE 3B: DISPATCH TRACKING                                │
│  Check status of previously dispatched issues               │
│  (issues set to "In Progress" on project board):            │
│    - Query GitHub for in-progress issues in this repo       │
│    - For each: check if closed, merged PR, QA passed,       │
│      or has new comments/activity since last check           │
│    - Report status changes in iteration log:                 │
│      "Dispatched #172 → PR merged, awaiting QA"             │
│      "Dispatched #165 → still in progress (no activity)"    │
│      "Dispatched #134 → completed, QA passed, closed"       │
│    - If a dispatched issue was completed by another session, │
│      verify: was QA submitted? Was issue closed properly?    │
│      Flag any issues that were closed without QA.            │
│                                                             │
│  Why: Dispatch is NOT fire-and-forget. The loop that         │
│  dispatched work is responsible for tracking it to           │
│  completion and reporting back to the operator.              │
│                                                             │
│  PHASE 4: WAIT                                              │
│  Sleep 5 minutes (300 seconds)                              │
│  Then go back to PHASE 1                                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Big Work Lifecycle (Async via Slack)

**No terminal presence assumption.** The owner may not be watching. All persistent communication goes through Slack.

When a deferred item needs clarification or approval:

1. **Post questions as a reply** in the original `:hourglass:` Slack thread
2. **Tag the owner** in the reply
3. **Each loop iteration** scans `:hourglass:` threads for owner responses:
   - Owner approved → remove `:hourglass:`, implement via CTO agent
   - Owner answered questions → update GitHub issue, re-evaluate scope
   - Owner said "not now" / "backlog" → keep deferred, stop asking
   - No response → wait (deadline rules apply per local override)
4. **Add `:eyes:` to each processed owner reply** in the thread (prevents double-processing)

**Why Slack, not terminal:** The owner may close the terminal session, miss questions in chat history, or not be present during autonomous loops. Slack threads are persistent and async — the owner responds when available, and the next loop iteration picks it up.

### Exit Conditions

| Condition | Action |
|-----------|--------|
| **3 consecutive empty scans** (15 min idle) | Log summary in chat, exit silently |
| **User returns** ("stop", "I'm back", Ctrl+C) | Log summary in chat, exit silently |
| **Critical failure** (deploy fails, API down) | Log summary + blocker details, exit |
| **Context window pressure** | Log summary, exit (user re-invokes in fresh session) |

**No Slack announcements for session start/end.** Only post to Slack for actual work items.

### 🚨 Scan Integrity Rule

Each scan iteration MUST actually execute its phases. "Empty scan" means Phase 1 returned 0 new items AND Phase 2 found 0 actionable issues — both phases must RUN.

Logging a scan as "empty" without executing its phases is a stop hook violation. The 3-empty-scan exit requires 3 REAL scans (tools called, results evaluated), not 3 logged entries.

**Anti-pattern (real incident, Mar 2026):** Session logged "scans 2 and 3 as also empty since the issue set is static" without calling `conversations.history`, `conversations.replies`, or `gh issue list`. Two scans were fabricated — no tools were invoked. The 3-empty-scan exit condition was met on paper but not in reality.

### Iteration Logging

After each iteration, maintain a running log in chat. Keep it incremental — just what happened THIS iteration:

```
── Iteration 1 (14:30) ──────────────────────
• Fixed #134 "caption not saving" → tests passed → deployed
• Closed #131 (verified by reporter)
• Dispatched: #172 (search rearchitecture), #165 (train button)
⏳ Waiting 5 minutes...

── Iteration 2 (14:35) ──────────────────────
• No new bugs
• Dispatch tracking: #172 → PR opened by dev agent | #165 → no activity yet
⏳ Waiting 5 minutes... (idle: 1/3)
```

### Session Summary (On Exit)

Log summary in chat only (NO Slack post). **This is the ONLY full summary — do NOT repeat what iteration logs already said. No duplication.**

**🚨 MANDATORY: Include timestamps** — the chat interface does NOT show when messages were generated. The user may read this summary hours later and needs to know exactly when the session ran and when this summary was produced.

**Two sections — Development vs Logistics:**

```
Dev loop complete. N iterations. (YYYY-MM-DD HH:MM — HH:MM local)

**Development:** Fixed #134 (caption saving), #135 (billing). Dispatched #172 (search rearchitecture) — merged, QA submitted. Closed #131 (verified).
**Logistics:** Pinged QA_PERSON on 5 overdue QA items. Deployed 2 pushes (CI success, Vercel Ready). Merged 2 stale worktrees from morning session.
No activity: 0 new bugs, 0 QA responses

_Summary generated: YYYY-MM-DD HH:MM local_
```

**Category definitions:**
- **Development** = issue work: fixes, features, dispatches, closures, QA submissions — what issues moved forward
- **Logistics** = operational overhead: QA pings, Slack scans, worktree merges, deployment verification, housekeeping commits, stale cleanup

**Format rules:**
- First line: session time range (start — end)
- Last line: when this specific summary text was generated (use system clock at time of writing)
- **Lead with Development** (the value-producing work), then Logistics
- **"No activity" = one line** for empty scans (0 new bugs, 0 QA responses, etc.)
- **Skip empty categories entirely** — no "Fixed: none", "Closed: none"
- **Don't mention irrelevant assessments** — issues you looked at but aren't actionable are noise the reader doesn't care about
- **Deferred items — show once, never duplicate:**
  - **"Deferred (NEW):"** only when items were first deferred THIS session
  - **"Deferred (from before):"** for carried items from previous sessions
  - If neither category has items, skip the line entirely

### Persistent Session Log

Session logs are ephemeral (in-chat only) by default. To enable cross-session continuity, persist a compact summary to the project's auto memory directory.

**File:** `memory/dev-loop-log.md` (in the project's auto memory directory, e.g., `~/.claude/projects/{project-hash}/memory/`)

**On session start:**
1. Read `memory/dev-loop-log.md` if it exists
2. Use the last 2-3 sessions as context: pending QA items, deferred work, dispatch tracking, stalled issues
3. This avoids re-scanning already-fixed issues or losing track of dispatch status
4. **Stale deferred re-evaluation:** If any item has been "Deferred (from before)" for 3+ consecutive sessions, re-read the FULL issue body and re-classify: is it genuinely blocked (unclear spec, needs owner decision), or was it wrongly categorized as blocked when it's actually dispatchable? Stale deferrals rot — they become self-reinforcing assumptions that no session challenges.

**On each iteration end (POKA-YOKE — crash-safe persistence):**
1. Overwrite the CURRENT SESSION's entry in `memory/dev-loop-log.md` with the latest cumulative state
2. This ensures the log survives session crashes, context pressure exits, or terminal closures
3. On session exit, the final iteration write IS the permanent record — no separate exit step needed

**Entry format (overwritten each iteration, prepended as newest-first):**
1. Update the current session entry in `memory/dev-loop-log.md`
2. Use this compact format — **only include categories that have content, skip empty ones:**
```
## YYYY-MM-DD HH:MM — HH:MM Trigger Name (N iterations, ~duration)
**Development:** Fixed #N (brief desc), dispatched #M, closed #K (verified)
**Logistics:** Pinged QA_PERSON, deployed N pushes (CI success, Vercel Ready), merged stale worktrees
**No activity:** 0 new bugs, 0 QA responses
**Deferred (NEW):** #N (reason)
**Deferred (from before):** #N (reason), #M (reason)
**Lessons:** (brief)
**Last updated:** YYYY-MM-DD HH:MM local
```
- Header: `HH:MM — HH:MM` = session start — session end (or "ongoing" if mid-session)
- Footer: `Last updated` = system clock at time of writing this entry (crash-safe: if session dies, this shows when the last iteration completed)
- **Development** = issue work: fixes, features, dispatches, closures, QA submissions
- **Logistics** = operational overhead: QA pings, Slack scans, worktree merges, deployment verification, housekeeping
- **No activity** = one line for empty scans. Skip if there WAS activity
- **Skip empty categories** — no "Fixed: none", "Closed: none", "Deferred (NEW): none"
- **Don't log irrelevant assessments** — issues you looked at but aren't actionable are noise
3. **Rotation:** If the file has more than 10 session entries, trim the oldest ones.

**Why:** Without persistence, each session starts blind — re-discovers the same pending QA items, re-checks stalled dispatches, and loses track of what was deferred. 30 seconds of writing on exit saves 5+ minutes of redundant scanning on next start.

---

### 🚨 PHASE 5: Big Work Enrichment (Before Exit)

**When the session has deferred items (big work), do NOT just list them and exit.** Before exiting, run an enrichment pass to make deferred issues dispatchable for a future session.

**Procedure:**

1. **Read every deferred GitHub issue thoroughly** — body, all comments, linked Slack threads
2. **Analyze what's already answered** vs what's genuinely ambiguous
3. **Only then ask the owner** about genuine gaps that would block implementation
4. **Update GitHub issues** with the owner's answers so future sessions have full context

**Rules:**
- **Exhaust existing context first.** If the answer is in the issue body or comments, do NOT ask the owner. This wastes their time and signals you didn't read.
- **Ask specific product decisions**, not implementation details you can figure out yourself.
- **Batch questions per issue** — one prompt per issue, not one per question.
- **If an issue has zero gaps**, report it as "ready to dispatch" and offer to dispatch immediately.

**Why this matters:** Deferred issues rot. Without enrichment, the same questions get re-asked every session, the owner gets frustrated, and issues stay blocked indefinitely. One enrichment pass during the owner's presence can unblock weeks of autonomous work.

**Flow:**
```
Exit condition met (3 empty scans / user returns)
  │
  ▼
Any deferred big work items this session?
  │
  ├─ NO → Log session summary, exit
  │
  └─ YES → For each deferred item:
            1. Read GitHub issue + all context
            2. Identify genuine gaps (not already answered)
            3. Present to owner: "These N issues are ready, these M need decisions"
            4. Ask gap questions for blocked items
            5. Update GitHub issues with answers
            6. Dispatch any newly-unblocked items if owner approves
            7. THEN log session summary and exit
```

### 🚨 "Dispatch" = Spawn Implementation, Not Bookkeeping

**Dispatch means starting actual work.** Adding a comment to a GitHub issue and moving it to "In Progress" on the board is NOT dispatch — it's bookkeeping.

**Real dispatch:**
1. Spawn CTO Agent (`strategic-cto-planner`) with the issue context
2. CTO orchestrates developer agent to write code
3. Code is committed, tested, deployed, QA submitted
4. Issue status is updated based on **actual outcomes**, not intent

**Fake dispatch (NEVER do this):**
- Adding "Dispatched for Implementation" comments to issues
- Moving issues to "In Progress" without spawning any agent
- Reporting issues as "dispatched but not started"

**If you cannot spawn implementation right now** (context pressure, session ending, too many items), be honest:
- Label it "ready-to-implement" (not "In Progress")
- Log: "Enriched and ready for next session to implement"
- Do NOT call it "dispatched"

**Why this matters:** Calling bookkeeping "dispatch" creates false status. The owner thinks work is happening. It isn't. The next session sees "In Progress" and assumes someone else is working on it. The issue rots in fake-progress limbo — worse than being honestly backlogged.

### 🚨 Investigation Depth — The Loop Is a Dispatcher, Not an Investigator

**In Continuous Scan Mode, the loop orchestrator's job is TRIAGE and DISPATCH.** Deep codebase investigation (reading multiple source files, tracing data flows, analyzing component hierarchies) belongs to the dispatched CTO/developer agent — not the loop.

**Why this matters:** Investigation consumes context tokens. The loop orchestrator has a finite context window shared across ALL issues in the iteration. Every file read, every grep, every "let me understand the architecture" burns tokens that could dispatch 3 more issues. Worse: the dispatched agent re-investigates anyway in its own context — making the loop's investigation pure waste.

**Anti-pattern (real incident, Mar 2026):**
```
Session 1: Fix bug → Investigate #124 (read 6 files, trace data flow,
           analyze component hierarchy) → "needs schema change, defer"
Session 2: Fix bug → Investigate #124 AGAIN (fresh context, no memory)
           → same conclusion → defer
Session 3: Same. #99 (clear spec, no blockers) never gets dispatched
           because investigation of #124 eats the context budget every time.
```

**The rule:**

| Issue type | Loop orchestrator does | Dispatched agent does |
|------------|----------------------|----------------------|
| **Small bug** (1-3 files, clear cause) | Investigate + fix directly | N/A |
| **Feature / medium+ scope** | Read issue body only → dispatch | Full investigation + implementation |
| **Unclear spec** | Read issue body → label needs-info → skip | N/A |

**Shallow triage checklist (what the loop MAY do):**
- Read the GitHub issue body and comments
- Check labels and dependencies
- One quick grep to confirm a file/function exists (if needed for classification)
- Classify scope: small bug → fix directly, everything else → dispatch

**Deep investigation (what the loop must NOT do for non-small-bug issues):**
- ❌ Reading multiple source files to understand architecture
- ❌ Tracing data flows across components
- ❌ Analyzing "how would I implement this"
- ❌ Checking if a schema change is needed
- ❌ Exploring alternative implementation approaches
- ❌ Any investigation that takes >2 tool calls beyond reading the issue

**If you catch yourself reading a 3rd source file for a non-bug issue, STOP.** You're investigating, not triaging. Dispatch to CTO agent with the issue body and let it investigate in its own context.

---

## TDD Requirements — Red-Green-Refactor

**The purpose of TDD is to PROVE the fix works before any human touches it.** Running `npm test` with passing unrelated tests is NOT TDD — that just proves you didn't break other things.

### The Three Phases (MANDATORY)

| Phase | What | Anti-pattern |
|-------|------|--------------|
| **RED** | Write/identify a test that reproduces the bug or validates the feature. Watch it FAIL. | Skipping straight to fixing code |
| **GREEN** | Implement the fix. Watch the test PASS. | "Tests pass" when no new test was written for the fix |
| **REFACTOR** | Clean up if needed. All tests still pass. | Shipping without running the full suite |

### What "Verify the Fix" Means

**A test or verification that proves the fix works is NOT optional.** Choose the appropriate level:

| Situation | Verification Type | Example |
|-----------|-------------------|---------|
| Logic bug (parsing, validation, calculation) | Unit test | `expect(parseResponse(malformed)).toEqual(fallback)` |
| API behavior (timeout, error handling, response) | Integration test | `expect(await callEndpoint(largePayload)).resolves.within(50000)` |
| Infrastructure/env-specific (can't automate) | Manual verification + evidence | `curl` the endpoint with realistic data, measure response time, include result |
| UI behavior | E2E test (Playwright) | Per project's e2e-testing skill |
| Performance/timeout | Math check + measurement | Calculate: input_size x processing_speed vs timeout_limit |

### The Verification Gate (Before QA Submission)

**Before submitting to QA, you MUST have evidence that the fix works:**

1. **Automated test written and passing** — preferred
2. **Manual verification performed and documented** — acceptable when automation isn't feasible (include the evidence: curl output, response time, screenshot)
3. **Mathematical proof** — for performance/timeout issues, show the math works

### UX Self-Assessment (UI Changes Only)

When a change touches **any user-facing UI** (components, pages, forms, labels, buttons), run this checklist BEFORE submitting to QA:

| Check | What to verify | Anti-pattern caught |
|-------|---------------|---------------------|
| **Labels are self-explanatory** | Every field label clearly communicates its purpose without needing documentation | "Post Name" when it's actually an internal codename |
| **Required indicators are accurate** | Red asterisks (`*`) only appear on fields that can actually be empty. Pre-filled or always-populated fields should NOT show required markers | Asterisk on a field that has 4 defaults pre-selected |
| **Purpose tooltips on non-obvious fields** | Fields whose purpose isn't immediately clear from the label have inline hints or tooltips explaining what they affect | "Priority" field with no indication of whether it affects AI output or is just for team coordination |
| **Placeholder text is instructive** | Placeholders show realistic examples, not generic descriptions | `"Enter text here"` vs `"e.g., Emphasize teamwork, keep it under 200 words"` |
| **No jargon without context** | Internal/technical terms visible to users include brief explanations | Showing "bundling_status" instead of "Processing Status" |
| **Actions have clear consequences** | Buttons and actions communicate what will happen when clicked | "Submit" with no indication of where/how it submits |

| **Visual screenshot check** | For layout/visualization changes, take a screenshot and review it yourself BEFORE showing to user or submitting QA | Shipping 4 UI iterations without once looking at the result — user has to say "take a screenshot and look at it yourself" |

**This is a self-check, not a QA replacement.** QA still validates UX — but these are anti-patterns Claude can catch before wasting a QA cycle.

**🚨 Screenshot-before-ship rule (visualization/layout changes):** When implementing visual features (dashboards, maps, charts, layout changes), take a playwriter screenshot and review it yourself AFTER each deploy. Do NOT present visual work to the user without first verifying it looks correct. This prevents multi-round "too big / too small / still broken" ping-pong.

**Anti-pattern (real incident, Mar 2026):** Factory Map went through 8+ scaling iterations because the developer never took a screenshot to verify sizing before presenting to the user. Each round required a deploy + wait + user review + feedback. A single self-check screenshot after the first deploy would have caught "way too big" or "way too small" immediately.

**INSUFFICIENT evidence (will result in wasted QA cycles):**
- "npm test passes" alone — proves no regression, NOT that the fix works
- "Config changed, should work now" — proves nothing without functional verification
- "Deployed, please test" — human QA is for UX/acceptance, not basic functionality

**If you cannot verify the fix works yourself, DO NOT submit to QA.** Investigate further. The human QA tester checks UX and acceptance — they are NOT your test runner for "does it work at all?"

### Anti-Pattern: Shipping Unverified Fixes to Human QA

**Real example (Issue #184 — Longs generation timeout):**

The autonomous bot changed timeout configuration **4 times**, ran `npm test` each time (all green), deployed and submitted to human QA each time. The human reported failure each time. 4 wasted QA cycles because the bot never once verified the endpoint actually completes within the timeout.

| Attempt | "Fix" | Verification | Result |
|---------|-------|-------------|--------|
| 1 | `.catch()` on JSON parse | `npm test` ✅ (unrelated) | QA fail: "Failed to fetch" |
| 2 | `maxDuration=60` | `npm test` ✅ (unrelated) | QA fail: "Generation failed:" |
| 3 | Move config to `vercel.json` | `npm test` ✅ (unrelated) | QA fail: 504 timeout |
| 4 | Timeout 60s→50s | `npm test` ✅ (unrelated) | QA fail: 504 timeout |

**What should have happened:**
1. **Measure** actual endpoint response time with realistic data (large transcript)
2. **Compare** against timeout limit: if `response_time > timeout`, config changes won't help
3. **Identify root cause**: data too large for time budget → reduce input size, not adjust timeout
4. **Fix root cause**, verify endpoint responds successfully, THEN submit to QA

**The rule:** If your fix is "change a number in config," you MUST verify the system actually works with that number before shipping.

### Standard TDD Checklist

| Step | Required |
|------|----------|
| Run existing test suite | Always (catch regressions) |
| Write/update test for THIS specific fix | When fix touches testable logic |
| Verify the fix works (test, curl, math) | **ALWAYS — no exceptions** |
| All tests pass | Must pass before deploy |
| Evidence in QA submission | Always (what you tested, what you observed) |

For `approval-required` repos, the test results AND verification evidence are part of the package presented to the developer before they approve deployment.

---

## Deployment Policy Awareness

The loop reads `Deploy mode` from the repo's bug-intake config and adjusts behavior:

### `auto-prod` (e.g., ProjectA)
- No stage environment. Tests pass locally → deploy to production.
- Fast iteration. Acceptable risk because team is the only user.

### `auto-stage` (e.g., ProjectB)
- Stage environment exists. Default branch = stage.
- Push automatically after tests pass. Breaking stage is fine — fast iteration.

### `approval-required` (e.g., YourCompany)
- Production with live clients. No stage environment (yet).
- **The loop still starts automatically** — bugs are scanned, triaged, fixed, and tested without asking.
- **Deployment requires explicit developer approval.** Present:
  1. What was fixed
  2. Test results (pass/fail)
  3. Files changed
  4. Ask: "Deploy to production?"
- Developer says yes → deploy. Developer says no → log and move on.

---

## Actionable Slack Replies Only

Every Slack reply MUST be actionable. Never leave things hanging.

**If fix is deployed and needs testing:** Tell the reporter exactly what to test.
  - "Fix deployed. Please test: [specific steps]. Reply here if still broken."

**If deferred:** Be clear about what happens next.
  - "Noted for owner review. No action needed from you."

**Never:** Post vague "tracking this" messages that leave people wondering if they need to do anything.

---

## Wait Implementation

```bash
# 5-minute wait between iterations
sleep 300
```

Or on Windows:
```powershell
Start-Sleep -Seconds 300
```

---

## Response Deadline (Verification)

If a reporter hasn't confirmed or rejected a fix within **2 days**, @mention them in the thread asking for verification. This is checked on every scan.

Local overrides may add additional response deadline behavior (e.g., escalating to a manager).

---

## Local Override Pattern

Repos can provide `.claude/skills/dev-loop-override/SKILL.md` to extend this skill. The `-override` suffix makes the inheritance relationship explicit in the skill list. Common overrides:

- **Additional scan targets** (e.g., separate QA channel)
- **Dedicated QA person** (e.g., always mention a specific team member)
- **Custom escalation** (e.g., escalate to CEO after N days)
- **Modified exit conditions** (e.g., different idle threshold)
- **Custom test steps** (e.g., E2E tests for UI changes)

The override pattern works the same as bug-intake: `## Override:` sections augment global steps, never skip them.

---

## Related Skills

| Skill | Location | Relationship |
|-------|----------|-------------|
| `autonomous-issue-dispatch` | `~/.claude/skills/` | Parent architecture — defines the full pipeline |
| `bug-intake` | `~/.claude/skills/` | **Core** — Each scan iteration runs bug-intake |
| `issue-dispatcher` | `~/.claude/skills/` | **Downstream** — Dispatcher triages issues |
| `slack` | `~/.claude/skills/` | Underlying API patterns for Slack operations |
| `qa-submission` | `.claude/skills/` (per-project) | Used by overrides that have separate QA channels |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artymclabin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
