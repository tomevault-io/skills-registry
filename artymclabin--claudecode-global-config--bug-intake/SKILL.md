---
name: bug-intake
description: Generalized Slack bug intake. Scans bug-report channels, auto-fixes small bugs, dispatches big work with clear specs, defers only genuinely ambiguous items to owner. Uses emoji reactions to track state. Each repo provides workspace config via local bug-intake skill. Supports shared channels where multiple repos receive bugs from one Slack channel via semantic routing. Use when this capability is needed.
metadata:
  author: artymclabin
---

# Bug Intake (Generalized)

> **Part of:** Autonomous Issue Dispatch System
> See `~/.claude/skills/autonomous-issue-dispatch/SKILL.md` for full architecture.

## Purpose

Scan Slack channels for bug reports across **any workspace** and:
1. **Small bugs** -> Fix immediately, communicate result
2. **Big work with clear spec** -> Create GitHub issue, dispatch via CTO agent
3. **Genuinely ambiguous** -> Create GitHub issue, defer to owner for clarification

This is the **generalized flow**. Each repo provides a local `.claude/skills/bug-intake-override/SKILL.md` with workspace config. Local skills extend this flow with `## Override:` sections — they never replace it.

**Trigger:** "Scan for bug reports" / "Run intake scan" / "Check bugs"

---

## Single-Thread Mode (Real-Time Trigger)

When invoked with a `thread_ts` parameter, process ONLY that specific thread instead of scanning the full channel. Used by the Slack Event Daemon for real-time bug response.

**Trigger:** "Run bug-intake in single-thread mode for thread_ts=X in channel Y"

**Behavior differences from full scan:**

| Aspect | Full Scan | Single-Thread |
|--------|-----------|---------------|
| Scope | All unprocessed messages in channel | One specific thread only |
| Emoji check | Scan skips messages with emoji | Daemon already added `👀` + `⚡` before dispatch |
| Step 2 | `conversations.history` | `conversations.replies?channel=CH&ts=THREAD_TS` |
| Step 9 | "Whose Turn?" scan on all unclosed threads | Skip — only the triggered thread |

**Flow:** Receive thread_ts + channel → fetch thread via `conversations.replies` → read all messages for context → triage (Step 4) → route (Step 5) → fix/deploy or defer (Step 6/8) → communicate (Step 7).

The `⚡` emoji indicates real-time daemon trigger (vs `👀` alone from cron). Functionally identical — observability only.

**Deduplication:** Daemon adds `👀` before dispatch. Cron sees `👀` and skips. No conflict.

---

## Config Resolution (Inheritance Model)

1. Read the local `.claude/skills/bug-intake-override/SKILL.md` in the current repo
2. If no local skill exists -> error: "This repo has no bug-intake config."
3. Load this global skill as the **base flow**
4. Apply local skill as **override** — local sections marked `## Override:` augment or replace the corresponding global step
5. All non-overridden global steps execute as-is

**Think of it like C++ inheritance:** The global skill is the base class. Local skills call `super()` (use the global flow) and add their specific behavior on top. Local overrides never skip global steps — they extend them.

---

## Workspace Config Schema

Each repo's local bug-intake SKILL.md must provide these values in a markdown table:

| Key | Required | Description |
|-----|----------|-------------|
| Workspace | Yes | Human-readable workspace name |
| Slack domain | Yes | For permalink construction (e.g., `yourcompanyhq` for `yourcompanyhq.slack.com`) |
| Bug reports channel | Yes | Channel name and ID |
| QA channel | No | Separate QA channel (ProjectA only). Omit for in-thread QA. |
| QA mode | Yes | `in_thread` (default) or `separate_channel` (override) |
| GitHub repo | Yes | `owner/repo` format |
| Deploy mode | Yes | `auto-prod`, `auto-stage`, or `approval-required` (see Deployment Policy) |
| Owner Slack ID | No | Slack user ID of the workspace owner/CEO. When present, submissions from this user are **auto-approved** — never defer back to owner on their own requests (Step 4 + Step 8). |

**Token resolution:** The `~/.claude/skills/slack/SKILL.md` skill defines which token to use per workspace. This skill does NOT specify tokens — the Slack skill handles that.

### Optional: Repo Routing (Legacy -- Single-Repo Config)

When a workspace has multiple repos sharing one bug channel, the local config can include keyword routing:

```
## Repo Routing
- "keyword1", "keyword2" -> owner/repo1
- "keyword3", "keyword4" -> owner/repo2
- default -> owner/repo3
```

The scan runs from ONE repo (the default), and routes issues to sibling repos based on keywords in the bug report.

For shared channels with semantic routing, use the Shared Channel Registry below instead. Legacy keyword routing is kept for backward compatibility.

---

## Shared Channel Registry

When one Slack channel serves multiple repos (e.g., a company-wide `#bugs` channel), routing is defined here at the global level. Claude reads each bug report and **semantically understands** which repo(s) it belongs to -- no keyword matching.

### CompanyHQ -- #bugs (`CHANNEL_ID_BUGS`)

**Workspace:** CompanyHQ | **Slack domain:** `yourcompanyhq`

| Repo | Local Path | GitHub | Handles | Deploy Mode |
|------|-----------|--------|---------|-------------|
| CompanyProject_AI | `~/repos/CompanyProject_AI` | `YOUR_USERNAME/CompanyProject_AI` | AI chatbot responses, auto-replies, n8n workflows, customer service bot behavior, AI knowledge base accuracy, CRM_TOOL integration | `auto-prod` |
| CompanyProject_WPSite | `~/repos/CompanyProject_WPSite` | `YOUR_USERNAME/yourcompany-site` | WordPress dashboard (logged-in users only), user registration/login, payment processing backend, CRM, CRM_TOOL/WhatsApp integrations, automations, Trello powerups, mentor management, trial lesson scheduling, admin tools | `approval-required` |
| CompanyProject_NextJS | `~/repos-secondary/CompanyProject_NextJS` | `YOUR_USERNAME/CompanyProject_NextJS` | ALL public-facing pages (logged-out users), Next.js site, Payload CMS, landing pages, pricing pages, website UI/UX, SEO, course/learning path content, instructor profiles, portfolio, blog, testimonials, FAQ, multi-language (EN/HE), media optimization | `approval-required` |
| CompanyProject_Docs | `~/repos-secondary/CompanyProject_Docs` | `YOUR_ORG/sops-docusaurus` | Internal documentation site, SOPs, procedures, onboarding docs, Docusaurus static site, knowledge base content | `approval-required` |
| CompanyProject_ChromeExt | `~/repos/CompanyProject_ChromeExt` | `YOUR_ORG/CompanyProject_ChromeExt` | Internal Chrome extension for company staff workflows, productivity tools, browser-based work utilities | `approval-required` |

### CompanyHQ -- #company-admin (TBD — channel to be created by ADMIN_PERSON)

**Workspace:** CompanyHQ | **Slack domain:** `yourcompanyhq`

| Repo | Local Path | GitHub | Handles | Deploy Mode |
|------|-----------|--------|---------|-------------|
| company-admin | N/A (no local clone) | `YOUR_ORG/company-admin` | Internal admin/ops tasks, process improvements, operational issues, team workflow requests — NOT bugs (general issue creation) | `approval-required` |

**Note:** This channel handles general issue creation (tasks, requests, improvements), not just bugs. Issue titles should NOT use "Bug:" prefix — use the message subject directly.

### ProjectB -- #bugs-general (`CHANNEL_ID_BUGS_2`)

**Workspace:** ProjectB | **Slack domain:** `projectb-hq`

| Repo | Local Path | GitHub | Handles | Deploy Mode |
|------|-----------|--------|---------|-------------|
| ProjectB_Bot | `~/repos-secondary/ProjectB_Bot` | `YOUR_USERNAME/ProjectB_Bot` | WhatsApp AI assistant, message handling, AI response quality, conversation history, authorized user auth, group chat interactions, media processing, bot commands (/summarize), OpenRouter API, Supabase user authorization | `approval-required` |
| ProjectB_Website | `~/repos-secondary/ProjectB_Website` | `YOUR_USERNAME/ProjectB_Website` | SaaS dashboard, user authentication, organization/team management, group chat associations, user invitations, billing, settings, password reset, email verification, role-based permissions, data source imports | `approval-required` |

**Note:** `projectb-schema` is a shared dependency — never create issues there directly. Issues belong in ProjectB_Bot or ProjectB_Website; schema changes happen as part of fixing those issues.

### ProjectB -- #hook-problem-reports (`CHANNEL_ID_HOOKS`)

**Workspace:** ProjectB | **Slack domain:** `projectb-hq`

Same repo routing as #bugs-general above. This channel captures webhook/integration failures specific to the bot's hook system.

### Semantic Routing Rules

1. **Read the full bug report** -- understand what system/feature is affected, not just surface keywords
2. **Match against "Handles" descriptions** -- which repo's domain covers the reported issue?
3. **Single-repo bugs (most common):** Create issue in the matched repo only
4. **Cross-repo bugs:** When a bug spans multiple systems (e.g., "chatbot sends wrong payment link" = CompanyProject_AI response + site payment page), create issues in ALL affected repos with cross-references:
   ```
   ## Cross-Reference
   Related issues: YOUR_USERNAME/CompanyProject_AI#N, YOUR_USERNAME/yourcompany-site#N
   ```
5. **Ambiguous bugs:** If genuinely unclear which repo handles it, create the issue in the most likely repo and add a `needs-triage` label. Mention the ambiguity in the issue body.
6. **Unknown repos:** If the bug doesn't match any repo in the registry, report it to the operator: "Bug about [X] doesn't match any registered repo. Skip or create in [suggested repo]?"

### Dispatch Behavior (RepoCoordinator)

RepoCoordinator is the for-loop wrapper — it runs the same bug-intake flow across multiple repos. The effect must be identical to running bug-intake from inside each repo individually.

**Step A: Scan Shared Channel Registry** (multi-repo channels above)
1. Scan each registered channel for unprocessed messages (same as Step 2)
2. For each message, determine target repo(s) via semantic routing
3. Mark as processing (`:eyes:`) immediately
4. Create GitHub issue in the matched repo(s) per Step 5

**Step B: Check repo-local overrides (direct path check, NOT filesystem scan)**

🚨 **Do NOT use `find`, `Glob`, or `Explore` to discover override files.** The repos are already listed in the Shared Channel Registry above with explicit `Local Path` values. Check each known path directly:

```bash
# Check known repos from registry — O(n) direct checks, no recursive scan
for repo_path in "~/repos/CompanyProject_AI" "~/repos/CompanyProject_WPSite" "~/repos-secondary/CompanyProject_NextJS" "~/repos-secondary/ProjectB_Bot" "~/repos-secondary/ProjectB_Website" "~/repos/MyProjectA"; do
  test -f "$repo_path/.claude/skills/bug-intake-override/SKILL.md" && echo "$repo_path"
done
```

1. For each repo that HAS an override, read its Workspace Config table — it may define additional channels not in the Shared Channel Registry
2. Scan those channels using the local override's rules (QA mode, emoji nuances, communication overrides, etc.)
3. Apply all `## Override:` sections from the local skill on top of the global flow

**Result:** Repos without local overrides get the global flow only. Repos with overrides (e.g., separate QA channels, custom communication steps) get the full augmented flow.

**CompanyProject_AI special rule:** External dispatch only — handle the Slack-routed issue, do NOT auto-trigger sheet scanning

---

## Windows Poka-Yoke (Mandatory for all Slack API calls)

🚨 **ALL Python commands that process Slack data MUST include these safeguards:**

1. **Encoding:** Set `PYTHONIOENCODING=utf-8` before any Python command. Slack messages contain emoji/Hebrew that crash Python's default cp1252 on Windows.
2. **Token export:** After `source ~/.claude/.env`, ALWAYS run `export SLACK_COMPANY_TOKEN SLACK_PROJECTB_TOKEN SLACK_BRAND_TOKEN` before Python subprocesses. `source` sets shell vars but Python's `os.environ` only sees `export`ed vars.
3. **Temp paths:** Use `$TEMP` (not `/tmp/`) for temp files on Windows, or Python's `tempfile.gettempdir()`.
4. **Heredocs for inline Python:** Use `python3 << 'EOF'` (single-quoted heredoc) to avoid bash variable/escaping conflicts. NEVER use `\!=` or `declare -A` in embedded Python.
5. **Separate Slack from GitHub:** NEVER combine Slack curl + `gh` CLI in the same Bash command. The `gh issue` hook blocks the entire command, including valid Slack calls.

### Automation Consideration

When a Slack scanning pattern is used **3+ times across sessions** with identical logic (same API call, same parsing, same output format), consider extracting it to a reusable script in the calling repo's `scripts/` directory. Ad-hoc inline Python is fine for one-off or evolving patterns — but stable, repeated operations should be scripted to eliminate encoding/token/escaping bugs.

**Current candidates for extraction** (stable patterns seen in 5+ sessions):
- Channel history scan + unprocessed message filtering
- Thread follow-up "whose turn" classification
- Emoji reaction add/remove

**NOT candidates** (context-dependent, evolving):
- Triage classification (needs LLM judgment)
- Semantic repo routing (needs LLM judgment)
- Thread reply composition (varies per bug)

---

## Generalized Flow

### Step 1: Load Config

```
Read local .claude/skills/bug-intake-override/SKILL.md
Extract workspace config table values
Resolve Slack token via ~/.claude/skills/slack/SKILL.md (workspace -> token mapping)
Check for ## Override: sections to apply after each global step
```

### Step 2: Scan for Unprocessed Messages

```bash
# Token resolved via slack skill (workspace -> token mapping)
curl -s "https://slack.com/api/conversations.history?channel=CHANNEL_ID&limit=50" \
  -H "Authorization: Bearer ${SLACK_TOKEN}"
```

**Unprocessed** = messages from humans (no `bot_id`) without any of these reactions: `:eyes:`, `:white_check_mark:`, `:hourglass:`

**Note:** `:white_check_mark:` messages are skipped here (new intake), but Step 9B re-checks their threads for follow-up replies that arrived after resolution. Resolved does not mean permanently ignored.

### Step 3: Mark as Processing

**IMMEDIATELY** add `:eyes:` reaction to prevent double-processing:

```bash
curl -s -X POST "https://slack.com/api/reactions.add" \
  -H "Authorization: Bearer ${SLACK_TOKEN}" \
  -H "Content-Type: application/json" \
  -d "{\"channel\":\"CHANNEL_ID\",\"timestamp\":\"MESSAGE_TS\",\"name\":\"eyes\"}"
```

### Step 4: Triage

**Small bug (fix immediately):**
- Clear error message or behavior
- Likely a single file fix
- No architectural changes

**Big work with clear spec (dispatch — NO owner approval needed):**
- Feature request with defined WHAT (expected behavior is described)
- Large scope but the reporter specified what they want
- Multiple files/systems but requirements are unambiguous
- 🚨 "Big" ≠ "blocked". Size alone is NEVER a reason to defer. If the spec says what to build, dispatch it.

**Genuinely ambiguous (defer to owner):**
- Reporter explicitly says "I don't have the full picture yet" or equivalent
- Contradictory requirements that need a product decision
- Missing critical information that only the owner can provide
- ONLY defer when the WHAT is unclear, never when the HOW is complex

🚨 **Owner auto-approval:** If workspace config has `Owner Slack ID` and the Slack message author matches that ID, the submission is **pre-approved by definition**. The owner IS the authority — never defer back to them. Route as "clear spec" (Step 8A) regardless of scope. If spec is genuinely unclear, ask clarifying questions in-thread instead of deferring.

🚨 **Staleness check (old reports):** Before dispatching a fix for a bug report older than 7 days, evaluate whether the issue is still relevant. Check: is the affected code/feature still the same? Has it been fixed by another commit? Ask in-thread if genuinely unclear. Do NOT blindly dispatch fixes for month-old reports without staleness evaluation.

### Step 5: Route to Correct Repo

**Shared channel (registry exists):** Use the Shared Channel Registry above. Semantically match the bug report against repo "Handles" descriptions. See "Semantic Routing Rules" for single-repo, cross-repo, and ambiguous cases.

**Single-repo channel (legacy):** If config has `## Repo Routing`, match keywords in the bug report text against routing rules. If no match, use the default repo.

Create the GitHub issue in the matched repo via `github-issue-manager` subagent:

🚨 **NEVER use `gh issue create` directly.** Global CLAUDE.md mandates the `github-issue-manager` subagent for ALL GitHub issue operations. A PreToolUse hook enforces this — direct `gh issue` calls WILL be blocked.

```
Task tool → subagent_type: "github-issue-manager"
  prompt: "Create issue in OWNER/REPO:
    Title: Bug: [Brief description]
    Body:
    ## Bug Report
    **Source:** Slack #CHANNEL_NAME
    **Reporter:** @username
    **Thread:** https://DOMAIN.slack.com/archives/CHANNEL_ID/pTIMESTAMP
    ## Description
    [Content of the Slack message]
    ---
    *Imported from Slack by bug-intake*"
```

### Step 6: Fix, Test, Deploy (Small Bugs)

**Autonomy rule:** Small bugs are auto-fixed without approval. No human sign-off needed — diagnose, fix, test, deploy, communicate.

**Pipeline:** Issue Handler (diagnose) → CTO Agent (implement) → Deploy

1. **Diagnose** via issue-handler: query logs, reproduce, build evidence package
2. **Hand off to CTO Agent** (`strategic-cto-planner`) — CTO orchestrates Developer, QA Engineer, Integration Tester. **Never dispatch directly to developer agent.**
3. **Run tests** — all existing tests must pass. If the fix touches testable logic, add or update tests. Tests must pass locally before any deployment.
4. **Deploy** according to the repo's `Deploy mode`:

| Deploy Mode | Behavior |
|-------------|----------|
| `auto-prod` | Deploy to production automatically after tests pass. No stage environment exists. |
| `auto-stage` | Push to stage/default branch automatically after tests pass. Stage can break — fast iteration. |
| `approval-required` | Tests must pass. Then present the fix + test results to the developer operating the loop. Deploy only after explicit approval. |

**TDD is non-negotiable.** Every fix must have passing tests before deployment, regardless of deploy mode. The difference is only *who approves the deploy* — the system or the human.

### Step 7: Communicate Result

**Universal rule:** Slack back-and-forth is only relevant when the bug reporter is NOT the person currently operating this Claude Code instance.

**Detection:** The person who invoked "scan for bugs" / "run intake" is the current operator. If the Slack message author matches, they're self-reporting.

#### Reporter IS the current operator

They reported a bug in Slack (e.g., from phone, or while chatting) and are now at the computer running Claude Code:
- Communicate directly in chat, NOT via Slack
- Add `:white_check_mark:` to Slack silently
- Tell the operator in chat: "Fixed [bug]. Deployed. You can verify at [URL]."

#### Reporter is NOT the current operator

Reply in the bug-report thread:

```bash
# Reply in thread
JSON=$(printf '{"channel":"%s","thread_ts":"%s","text":"%s"}' "CHANNEL_ID" "MESSAGE_TS" "Fix deployed. Please verify: [specific test steps]. Reply here if still broken.")

curl -s -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer ${SLACK_TOKEN}" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data "$JSON"
```

If the fix is clear-cut and doesn't need reporter verification:
- Replace `:eyes:` with `:white_check_mark:`
- Reply: "Fixed and deployed. [brief description]"

If verification is needed, ask in-thread and keep `:eyes:` until confirmed (see Step 9).

#### `separate_channel` override

If the local skill has `QA mode: separate_channel` and `## Override:` sections, apply those overrides after the global steps above. The local override adds additional steps (e.g., QA submission to a dedicated channel) without skipping the base flow.

### Step 8: Big Work

**8A: Clear spec (dispatch immediately):**
1. Keep `:eyes:` reaction (processing, not deferred)
2. Create GitHub issue
3. Reply in thread: "Tracked as issue #N. Dispatching for implementation."
4. Route to CTO Agent for implementation (same as Step 6 pipeline)
5. 🚨 **Report dispatch outcome back to thread.** After the dispatch completes (success or failure), reply in the Slack thread with the result: "Fix deployed for #N" or "Blocked on [reason] — see #N." Never leave a thread at "dispatching" without a follow-up status.

**8B: Genuinely ambiguous (defer to owner):**

🚨 **Poka-yoke:** If workspace config has `Owner Slack ID` and the message author matches, you MUST NOT reach 8B. Owner submissions are auto-approved (Step 4). If you're here with an owner message, go back to 8A.

1. **Remove `:eyes:`** if present, then add `:hourglass:` — deferred items MUST have `:hourglass:`, NEVER `:eyes:`. Using `:eyes:` on deferred items breaks the "Whose Turn" scanner (Step 9) because `:eyes:` threads expect a deployed fix, not an owner approval.
2. Create GitHub issue
3. Reply in thread: "Needs clarification. Created issue #N — what's unclear: [specific question]."
4. Report to current operator in chat

### Step 9: Thread Follow-Up Scanner ("Whose Turn?")

On every scan iteration, check ALL open threads using one universal rule: **whose turn is it to respond?**

This replaces the old emoji-gated Step 9A/9B/9C with a single mechanism that works regardless of which emoji is on the top-level message. No more bugs from wrong emoji causing the wrong scan path.

**9.0: Get all open threads**

```bash
# Get recent channel messages (top-level)
conversations.history channel=CHANNEL_ID limit=50

# Filter: threads WITHOUT :white_check_mark: on top-level = unclosed
# For each unclosed thread with replies (reply_count > 0):
conversations.replies channel=CHANNEL_ID ts=THREAD_TS
```

**9.1: Classify each thread by last responder**

For each unclosed thread, look at the **last message** in the thread:

| Last message from | Action |
|---|---|
| **Human** (no `bot_id`) without bot `:eyes:` reaction on that reply | **Bot's turn** — process this reply immediately (see 9.2) |
| **Human** with bot `:eyes:` reaction on that reply | Already processed — skip |
| **Bot** and < ping threshold since bot's message | **Human's turn** — waiting for response, skip |
| **Bot** and ≥ ping threshold since bot's message | **Overdue** — ping the expected responder (see 9.3) |

🚨 **This is emoji-agnostic.** Whether the top-level has `:eyes:` or `:hourglass:` doesn't matter. The only question is: who spoke last in the thread, and has the other side responded?

**9.2: Process human reply (Bot's turn)**

🚨 **IDENTIFY THE SPEAKER FIRST.** Before classifying the reply content, check the `user` field on the message to determine WHO replied. Never assume — check the actual user ID against known identities (owner, QA operators, reporters). The speaker determines the classification:
- **Owner** replying to a QA thread they weren't assigned to → this is owner feedback/process correction, NOT a QA response
- **QA operator** replying → this is a QA response (approval/rejection)
- **Reporter** replying → this is verification feedback
- **Someone not tagged in the thread** → they're providing unsolicited feedback; acknowledge but don't misclassify as QA

🚨 **RETAIN ORIGINAL ASSIGNEE.** When re-submitting a fix after rejection, tag the SAME person who was originally assigned (check the bot's original QA submission message in the thread). Never switch assignees — if QA_PERSON was the original tester, re-tag QA_PERSON, not QA_PERSON_B.

Read the reply content and classify semantically:

| Classification | Indicators | Action |
|---|---|---|
| **Approval / Confirmation** | "approved", "pass", "lgtm", "works", "confirmed", "looks good", "yes", "do it", "go ahead", "proceed", thumbs up, or similar affirmative | Close GitHub issue, replace top-level emoji with `:white_check_mark:`, add `:eyes:` to reply. **🚨 SILENT CLOSE — do NOT post a reply message.** Reactions only. No "Confirmed. Closing." — that's noise that wastes the confirmer's attention. |
| **Rejection / Still broken** | "rejected", "fail", "still broken", "doesn't work", "issue", "bug", error descriptions, screenshots of errors | Route back to fix → redeploy → reply in thread. Add `:eyes:` to reply |
| **Question** | "?", "where", "how", "can't find", "don't understand" | Investigate → answer in thread. Add `:eyes:` to reply |
| **Owner approval on deferred item** | Reply from owner in an `:hourglass:` thread, any affirmative | Remove `:hourglass:`, add `:eyes:` to reply. **Update GitHub issue:** remove `hourglass` label, add `owner-approved-explicitly` label, add comment with approval context. THEN dispatch for implementation (CTO Agent). 🚨 Acknowledging in Slack without updating GitHub = lost tracking. |
| **Owner decline** | "not now", "backlog", "later" | Keep `:hourglass:`, add `:eyes:` to reply. Log: "Owner deferred #N." |
| **Ambiguous** | Can't classify with confidence | Read full thread context. When genuinely unclear, ask in-thread: "Want me to proceed with this, or is there more context?" Add `:eyes:` to reply. |

**9.3: Ping overdue threads (Human's turn, no response)**

| Threshold | Action |
|---|---|
| 2 days since bot's last message | @mention the expected responder in-thread |
| 7 days (5 days after first ping) | Second @mention (final nudge) |
| After second ping | Accept as permanent state. Report as "stale unverified." No auto-closure ever. See `references/design-decisions.md` for rationale. |

The "expected responder" is whoever the bot's last message was directed at (QA person, reporter, or owner).

🚨 **Before pinging:** First run **QA Backlog Auditing** (below) to verify they genuinely haven't responded. Check emoji states + thread replies. Only then ping.

**9.4: Reopen check (`:white_check_mark:` threads)**

Separately, scan threads of resolved messages for **new human replies posted AFTER the bot's last message**. This catches follow-up bugs in threads we already marked as resolved.

🚨 **Content-awareness — filter before reopening:**
- **Confirmation replies** ("done", "fixed", "thanks", "works now", ✅ in text) → NOT a reopen. Leave checkmark.
- **New bug details** (new symptoms, "still broken", "also X doesn't work", additional context) → Genuine reopen. Process below.

🚨 **Autonomy rule:** Reopens are processed autonomously — same as Step 6. Do NOT defer to user. Report as Tier 1 (auto-handled), NOT Tier 2 (needs you).

If genuine reopen found:
1. Remove `:white_check_mark:`, add `:eyes:` (re-open for processing)
2. Read the new reply to understand the follow-up issue
3. Create GitHub issue with the follow-up details + Slack thread link
4. Reply in Slack thread: "Reopened — follow-up tracked as #N."
5. Route to triage (Step 4) — may be small bug, big work, or ambiguous

This loop applies universally. Local overrides may add additional processing (e.g., QA channel specifics) on top of this.

---

## Emoji State Machine (Universal)

| Emoji | Meaning | When to Add |
|-------|---------|-------------|
| `:eyes:` | **Processing lock** — claimed by a scan, prevents double-processing | Immediately on intake. Removed when resolved (replaced by `:white_check_mark:`) |
| `:white_check_mark:` | Resolved | Fix deployed and confirmed, or clear-cut fix deployed |
| `:hourglass:` | Deferred (big work, needs approval) | When triaged as big work |

**Unprocessed** = messages without any of these reactions.

**Thread reply emojis** (prevents double-processing):

| Emoji | On What | Meaning |
|-------|---------|---------|
| `:eyes:` | Individual thread reply | This reply has been read and acted upon |

When scanning threads, skip replies that already have `:eyes:` from the bot. Only process new (unreacted) human replies.

`:eyes:` is a **mutex**, not a status indicator. It means "a scan has claimed this message." It stays until the fix is verified, then gets replaced by `:white_check_mark:`. If the fix is clear-cut (no verification needed), skip straight to `:white_check_mark:`.

### Lifecycle

```
New bug (clear-cut)          -> eyes -> fix -> deploy -> remove eyes, add checkmark
New bug (needs verification) -> eyes -> fix -> deploy -> keep eyes + "please verify"
                                            -> reporter confirms -> remove eyes, add checkmark
Big work                     -> hourglass + defer to owner
```

---

## Bidirectional Linking

Always maintain links between Slack and GitHub:
- **Slack -> GitHub:** Include Slack thread permalink in GitHub issue body
- **GitHub -> Slack:** Reply in Slack thread with GitHub issue URL after creation

```
Thread permalink format:
https://{domain}.slack.com/archives/{channel_id}/p{ts_without_dot}
```

---

---

## QA Backlog Auditing

When auditing pending-qa issues against Slack QA channel:

**Always check reaction state before reporting backlog.** A QA submission with `:white_check_mark:` reaction means it was auto-closed — QA_PERSON's reply is NOT expected.

**Audit procedure:**
1. Get list of GitHub issues with `pending-qa` label
2. For each corresponding Slack QA thread, check:
   - Does thread have `:white_check_mark:` reaction? → Auto-closed, not waiting
   - Does thread have replies from QA_PERSON (non-bot user)? → Responded
   - Neither? → **Genuinely waiting**
3. Report only genuinely waiting items as backlog
4. Clean up: Remove `pending-qa` label from closed GitHub issues

**Common false positive:** Reporting all pending-qa issues as "waiting" without checking which Slack threads have checkmarks.

**Trust past closures:** Issues that were closed before the current scan session are trusted by default. Do NOT raise alarms about missing QA trail on pre-existing closures. Past ad-hoc decisions to skip QA or close without full process were made with context you don't have — trust them. Only re-examine if there's an active red flag (regression reported, new complaint, reopened by user).

---

## References

- `references/design-decisions.md` — Architectural rationale, operator philosophy, report triage rules, no-auto-closure policy. Read before proposing changes to verification logic or reporting format.

## Related Skills

| Skill | Location | Relationship |
|-------|----------|-------------|
| `autonomous-issue-dispatch` | `~/.claude/skills/` | Parent architecture — defines the full pipeline |
| `issue-dispatcher` | `~/.claude/skills/` | **Downstream** — Dispatcher triages issues created by Bug Intake |
| `dev-loop` | `~/.claude/skills/` | **Wrapper** — Dev Loop runs Bug Intake repeatedly in 5-min intervals |
| `slack` | `~/.claude/skills/` | API patterns, error handling for all Slack operations |
| `qa-submission` | `.claude/skills/` (per-project) | Separate QA channel submission after fix |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artymclabin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
