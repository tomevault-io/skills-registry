---
name: iterating-pr-reviews
description: Use after opening a PR (sprint or phase) when the project has Gemini Code Assist + OpenAI Codex bots configured for auto-review. Drives the wait-fetch-address-retrigger loop until comments stabilise. Alternates triggers across iterations, graduating each bot out of the rotation after its first clean review and treating two consecutive silent rounds as a broken-bot signal. Uses ScheduleWakeup to free the session between iterations. Use when this capability is needed.
metadata:
  author: tuvens
---

# Iterating PR Reviews

## When to use

Invoked by `implementing-as-sprint` and `orchestrating-as-phase` after a PR is opened against a real merge target. This skill drives the bot-feedback iteration loop so the agent — not the user — owns the feedback-to-fix cycle.

**Sprint 0 trial PRs are an exception.** Those PRs are `[DO NOT MERGE]` reference-only artefacts. Codex review of Sprint 0 PRs is user-driven (per `running-sprint-zero-trial/SKILL.md`). Do NOT enter this loop for Sprint 0 PRs.

---

## Precondition checks

Before starting:

1. Load `.claude/orchestration.yaml`. Verify `github_repo` is set and non-null. If missing, stop and tell the user to run `/update-orchestration-config` to add `github_repo`.
2. Confirm the PR number is known (passed in by the calling skill or visible in the current session context).
3. Confirm this skill is being invoked deliberately after PR creation — it is NOT auto-triggered after every commit.
4. Confirm the PR is NOT a Sprint 0 trial PR (title does not start with `[DO NOT MERGE]` or `[NOT FOR MERGE]`).

---

## The iteration loop

### Step 1 — Wait 10 minutes

Invoke `ScheduleWakeup` with `delaySeconds=600`. Set the `prompt` to a self-resuming value that re-enters this skill at Step 2, e.g.:

> "Resume iterating-pr-reviews for PR {{PR}} on repo {{github_repo}}. Start at Step 2: fetch bot comments."

This frees the user's session. The agent wakes automatically when the timer fires.

**Why 10 minutes?** PR review bots typically comment within 1–2 minutes on small PRs. Large PRs can take longer; 10 minutes is a safe upper bound for both Gemini Code Assist and OpenAI Codex.

### Step 2 — Fetch bot comments and recover state

```bash
gh api repos/{{github_repo}}/issues/{{PR}}/comments
gh api repos/{{github_repo}}/pulls/{{PR}}/comments
```

Filter results for author logins:
- `gemini-code-assist[bot]`
- `chatgpt-codex-connector[bot]`
- `claude[bot]` — included so Claude's review comments are picked up when `@claude review` is in the loop as a fallback (see Step 4.5).

Collect the full text of every comment from these authors. Record each comment with: author, comment ID, URL, and body.

**Recovering bot state across ScheduleWakeup invocations:** After fetching issue comments, scan all comments from the PR author (including yourself — the agent account that opened the skill) for the most recent one whose body starts with the line `<!-- review-state-v1`. Parse the key=value header block between the opening `<!--` and closing `-->` to recover the previous iteration's bot states. If no such comment exists, initialise both bots as `active` with `stalls=0`. The full format is documented in the [State persistence](#state-persistence) section.

### Step 3 — Categorize by severity

Apply the rubric below (also in `commands/review-phase-pr.md`):

| Severity | Label | Description |
|---|---|---|
| HIGH | P1 | Must fix before merge: security issues, data loss, broken acceptance criteria, incorrect logic |
| MEDIUM | P2 | Should fix or explicitly track: performance concerns, maintainability, missing error handling |
| LOW | P3 | Nice-to-have: style suggestions, minor naming, optional improvements |

### Step 4 — Address actionable comments

For each P1 and P2 comment:

**Option A — Fix it:**
1. Make the change in code.
2. Commit with a focused message referencing the bot comment.
3. Push to the PR branch.

**Option B — Reject it (with stated reason):**
1. Reply on the comment thread via:
   ```bash
   gh api repos/{{github_repo}}/issues/comments/{{comment_id}}/replies \
     --method POST --field body="Rejecting: <reason>"
   ```
   Or use a PR review comment reply as appropriate.
2. Document the rejection reason clearly. "Won't fix" without a reason is not acceptable.

**Option C — Defer to a future phase/sprint:**
1. Create a GitHub issue:
   ```bash
   gh issue create --repo {{github_repo}} \
     --title "<short description>" \
     --body "<detail from bot comment>" \
     --label "phase-{{N}}"
   ```
2. Add a row to `PHASE-ROADMAP.md`'s Issue Summary Table assigning the issue to the target phase.
3. Ensure that target phase's `ORCHESTRATION-PROMPT.md` (or the relevant sprint `PROMPT.md`) lists the issue in scope.
4. Post a reply on the original PR comment thread linking to the new issue.

All four steps are required for a valid deferral. See `docs/deferred-issues.md` for the full process.

P3 comments that are out of scope for the current sprint should be deferred following the four-step process in `docs/deferred-issues.md`. P3 comments that are genuinely not worth tracking (subjective style preferences, duplicate suggestions, etc.) may be rejected with a brief reply ("noted, out of scope / won't fix") — but every P3 comment must still be acknowledged.

**Never silently skip comments.** Every bot comment must result in one of: fix, rejection with reason, or tracked deferral.

### Step 4.5 — Determine which bots are active

#### Bot state machine

Each bot (Codex, Gemini) has one of three states:

| State | Meaning | When entered |
|---|---|---|
| `active` | bot is currently in the trigger rotation | initial state on PR open; or re-activated after new findings |
| `dropped-clean` | bot has given one clean review on the current HEAD; omitted from the next retrigger | after a clean review is confirmed (see below) |
| `dropped-broken` | bot has stalled twice without posting an active review marker; omitted from all future retriggers | when `stall_count >= 2` |

Start each new PR loop with both bots `active` and `stall_count=0`.

#### Clean review definition

A bot's review for the current HEAD is **clean** if BOTH of the following hold:

1. **Active review marker present.** The bot posted a review-marker comment after the most recent retrigger and referencing the current HEAD SHA:
   - **Codex:** a comment whose body starts with `### 💡 Codex Review` AND contains the substring `Reviewed commit: <current HEAD SHA>`.
   - **Gemini:** a comment whose body contains both "Gemini" and a `Reviewed commit:` reference OR any review-state submission from `gemini-code-assist[bot]` posted after the most recent retrigger and against the current HEAD. _Note: Gemini's exact marker format is unverified at the time of this writing. If the marker pattern above does not match, fall back to: any review comment from `gemini-code-assist[bot]` posted after the most recent retrigger counts as a review marker regardless of body format. Document the actual marker text observed in the tracking comment for future reference._
2. **Zero new P1 or P2 comments** from that bot posted since the most recent retrigger. P3 comments are allowed and do not block a clean classification.

#### Silent stall definition

After the 10-minute wait following a retrigger, if a bot in `active` state has **not** posted an active review marker on the current HEAD:
- Increment that bot's `stall_count`.
- If `stall_count >= 2`: transition the bot to `dropped-broken`.
- If the bot DOES post a marker (whether clean or with findings): reset `stall_count` to 0.

#### Re-activation from dropped-clean

A bot in `dropped-clean` can re-activate. After the wait in Step 6, check whether a `dropped-clean` bot posted new P1 or P2 comments on its own cadence (i.e., it re-reviewed without being triggered). If yes: transition that bot back to `active` and clear its clean status.

#### Usage-limit fallback

Before composing the retrigger comment, check whether either Gemini or Codex is **currently** usage-limited. A bot is **currently usage-limited** if both:

1. Its most recent comment **since the previous retrigger** (i.e., posted after `last_retrigger_ts` from the tracking comment, or after PR creation on the first cycle) is an explicit usage / quota / rate-limit message — e.g., "I've reached my usage limit for this period", "rate limited", "quota exceeded".
2. It has not posted a real review marker (`### 💡 Codex Review` for Codex, or any review-state submission for Gemini — see the [Clean review definition](#clean-review-definition)) since that usage-limit message.

**Scope the scan to the current cycle's window** — do NOT scan the full PR comment history. A historical limit that the bot has since recovered from must not stick: once a bot posts a real review marker (whether clean or with findings), the usage-limit overlay is cleared. Do NOT mark a bot as limited based on transient errors, empty replies, or unrelated comments — only an explicit limit-related message counts.

When a bot is currently usage-limited **and** in `active` state, substitute `@claude review` for that bot's slot in the retrigger comment for this cycle. The usage-limit substitution is an overlay: it does not change the bot's state-machine state, and the bot may resume on the next cycle if the limit clears. **Bots already in `dropped-clean` or `dropped-broken` are not in the retrigger rotation, so a usage-limit overlay does not apply to them** — there is no slot to fill.

Compose the usage-limit substitution using this table:

| Gemini | Codex | Usage-limit trigger substitution |
|---|---|---|
| available | available | no substitution (use state-machine table) |
| available | limited   | replace Codex slot with `@claude review` |
| limited   | available | replace Gemini slot with `@claude review` |
| limited   | limited   | `@claude review` (single line — do not duplicate) |

If `@claude review` substitutes for a limited bot, append a one-line note in the same comment body explaining which bot hit a limit, e.g.:

> `(Falling back to @claude review because gemini-code-assist[bot] reported a usage limit.)`

**Precondition:** `@claude review` requires the Claude GitHub App to be installed on the repo. If it is not installed and a fallback is needed, stop the loop and surface this to the user — do not silently leave actionable bot comments unaddressed.

### Step 5 — Re-trigger reviewers

After addressing all actionable comments, determine the retrigger comment body using the current bot states (after applying usage-limit overrides from Step 4.5):

| Codex state | Gemini state | Trigger lines |
|---|---|---|
| active | active | `@codex review` + `/gemini review` |
| active | dropped-* | `@codex review` |
| dropped-* | active | `/gemini review` |
| dropped-* | dropped-* | **no retrigger** — go directly to Step 8 (stop condition check) |

Post the retrigger comment via:

```bash
gh pr comment {{PR}} --repo {{github_repo}} --body "<trigger lines>"
```

All triggers appear on separate lines in the same comment body. Posting them together avoids two notification events and keeps the PR timeline tidy.

**After posting the retrigger comment, update the PR's review-state tracking comment** with the current bot states (format and update mechanism documented in the [State persistence](#state-persistence) section below). Do this before invoking `ScheduleWakeup` so the state is durably recorded.

If both bots are in a `dropped-*` state, skip posting a retrigger comment and proceed immediately to Step 8.

### Step 6 — Wait another 10 minutes

Invoke `ScheduleWakeup` again with `delaySeconds=600`. Self-resuming prompt:

> "Resume iterating-pr-reviews for PR {{PR}} on repo {{github_repo}}. Start at Step 7: re-fetch and compare."

### Step 7 — Re-fetch and compare

Repeat Step 2 (including recovering state from the tracking comment, and the `claude[bot]` filter if `@claude review` was triggered in Step 5). Compare newly fetched bot comments against the set already addressed in the previous iteration.

A comment is "new" if its comment ID was not present in the previous fetch. Only count comments posted AFTER the Step 5 re-trigger.

For each **`active`** bot, evaluate against the clean-review definition:
- **Marker present + zero new P1/P2:** clean review. Transition to `dropped-clean`. Record `last_clean_commit` (current HEAD SHA) and `last_clean_ts`.
- **Marker present + new P1/P2 found:** not clean. Keep `active`. Reset `stall_count` to 0. Return to Step 4 to address the new findings.
- **No marker present (silent stall):** increment `stall_count`. If `stall_count >= 2`, transition to `dropped-broken`. Otherwise keep `active` and proceed.

For each **`dropped-clean`** bot, check whether it posted new P1 or P2 comments since the last retrigger on its own cadence:
- **New P1/P2 found:** re-activate (transition back to `active`, clear `last_clean_commit`). Return to Step 4.
- **No new actionable findings:** remain `dropped-clean`.

Also re-run the Step 4.5 usage-limit check on the freshly fetched comments — a bot that was available in earlier iterations may post a usage-limit message in this round.

After evaluating all bots, update the tracking comment with the new states, then proceed to Step 8.

### Step 8 — Stop condition check

Evaluate the current states of all bots:

- **All bots `dropped-clean`:** loop terminated successfully. Return control to the caller with a merge-ready report listing which bots gave clean reviews and on which commits. Set `ready_to_merge: true` in the tracking comment.
- **All bots dropped (any combination of `dropped-clean` and `dropped-broken`) AND at least one is `dropped-clean`:** loop terminated, ready to merge. Note in the report which bots cleared and which were broken. Set `ready_to_merge: true` in the tracking comment.
- **All bots `dropped-broken` (none clean):** the review pipeline is broken. Do NOT silently proceed. Surface to the user: "Codex stalled N times, Gemini stalled M times — no bot gave a clean review. Options: wait for bots to recover, invoke `@claude review` as a fallback reviewer, or merge anyway with explicit acknowledgement." Set `ready_to_merge: false`. Halt the loop.
- **At least one bot still `active`:** continue the loop. Return to Step 4 (if new actionable comments were found in Step 7) or Step 5 (if no new findings but the bot is active due to a single stall only).
- **User explicitly requests a stop:** honour immediately. Report the current state of open comments and exit the loop regardless of bot states.

---

## State persistence

The skill writes and updates a single tracking comment on the PR after every cycle. This comment is the durable store for bot states across `ScheduleWakeup` invocations.

### Tracking comment format

```
<!-- review-state-v1
iteration: 3
last_retrigger_ts: 2026-05-14T15:23:00Z
codex: state=dropped-clean stalls=0 last_clean_commit=abc1234 last_clean_ts=2026-05-14T15:23:00Z
gemini: state=active stalls=1 last_clean_commit= last_clean_ts=
ready_to_merge: false
-->

### 🔄 Review iteration state (auto-maintained by iterating-pr-reviews)

| Reviewer | State | Stalls | Last clean commit |
|---|---|---|---|
| Codex   | dropped-clean | 0 | abc1234 (2026-05-14T15:23Z) |
| Gemini  | active        | 1 | — |

Next retrigger: `/gemini review` only.
```

`last_retrigger_ts` is the UTC timestamp of the most recent retrigger comment the skill posted (or the PR creation time if no retrigger has happened yet). It anchors the cycle-scoped scans for usage-limit messages and active review markers (see [Usage-limit fallback](#usage-limit-fallback) and [Clean review definition](#clean-review-definition)) — comments older than this timestamp are out of scope for the current cycle's checks.

The HTML comment block (`<!-- review-state-v1 … -->`) is machine-readable and must remain parseable. The human-readable table below it is for PR readers; keep both sections in sync.

### Writing and updating the tracking comment

**First write (no existing tracking comment):** post via the GitHub API directly so the response JSON gives you the comment ID for later PATCH calls. `gh pr comment` is the convenient wrapper but does NOT expose the comment ID in its output — using it for the initial post breaks the in-place update flow below.

```bash
COMMENT_JSON="$(gh api repos/{{github_repo}}/issues/{{PR}}/comments \
  --method POST \
  --field body="<full tracking comment body>")"
TRACKING_COMMENT_ID="$(printf '%s\n' "$COMMENT_JSON" | jq -r .id)"
```

**Subsequent updates:** PATCH the existing comment in-place using the captured ID (or the one recovered from the resume fetch — see below):

```bash
gh api repos/{{github_repo}}/issues/comments/$TRACKING_COMMENT_ID \
  --method PATCH \
  --field body="<updated tracking comment body>"
```

**Reading on resume:** when the skill resumes after `ScheduleWakeup`, it recovers state by fetching all issue comments and finding the most recent one from the PR author whose body starts with `<!-- review-state-v1`. The comment ID is the `id` field of that comment in the fetch response — use it for subsequent PATCH calls. Parse the key=value pairs from the header block to recover bot states.

### Field definitions

| Field | Type | Description |
|---|---|---|
| `iteration` | integer | increments each full cycle (Steps 2–8) |
| `last_retrigger_ts` | ISO-8601 UTC | timestamp of the most recent retrigger comment posted by the skill (or PR creation time on cycle 1). Anchors cycle-scoped scans for usage-limit messages and review markers. |
| `codex: state` | `active` \| `dropped-clean` \| `dropped-broken` | current Codex state |
| `codex: stalls` | integer | consecutive silent rounds without a Codex marker |
| `codex: last_clean_commit` | SHA or empty | HEAD SHA when Codex last gave a clean review |
| `codex: last_clean_ts` | ISO-8601 or empty | timestamp of the clean review |
| `gemini: state` | same as Codex | current Gemini state |
| `gemini: stalls` | integer | consecutive silent rounds without a Gemini marker |
| `gemini: last_clean_commit` | SHA or empty | HEAD SHA when Gemini last gave a clean review |
| `gemini: last_clean_ts` | ISO-8601 or empty | timestamp of the clean review |
| `ready_to_merge` | `true` \| `false` | set to `true` when the termination condition is met |

---

## What "addressed" means

A bot comment is considered addressed when ANY ONE of the following is true:

- **(a) Fixed:** a code change was committed and pushed to the PR branch that resolves the concern raised.
- **(b) Rejected:** a reply was posted on the comment thread explicitly explaining why the comment will not be actioned.
- **(c) Deferred to a future phase/sprint:** a GitHub issue was created with `gh issue create`, a row was added to `PHASE-ROADMAP.md`'s Issue Summary Table assigning the issue to the target phase, that target phase's `ORCHESTRATION-PROMPT.md` (or the relevant sprint `PROMPT.md`) lists the issue in scope, AND a reply was posted on the original PR comment thread linking to the new issue. All four steps are required for a valid deferral.

Addressing means closing the loop on every comment — the bot, the user, and future reviewers can all see the outcome.

---

## Sprint 0 exception

This loop does NOT run for Sprint 0 trial PRs.

Sprint 0 PRs are `[DO NOT MERGE]` reference artefacts. Their Codex review is manually invoked by the user (per `running-sprint-zero-trial/SKILL.md` — "After opening the PR, stop and report the PR URL. The user invokes Codex to review."). The iteration loop is reserved for merge-bound PRs only.

Detect Sprint 0 PRs by checking the PR title for `[DO NOT MERGE]` or `[NOT FOR MERGE]` prefix before entering the loop.

---

## Never

- Never silently skip a bot comment — every comment must be addressed (fix, reject, or defer).
- Never address a comment by adding code that doesn't fully resolve the concern (e.g., adding a TODO comment in place of a real fix counts as a silent skip).
- Never retrigger the bots without having addressed all P1 and P2 comments from the previous round.
- Never enter this loop for Sprint 0 trial PRs.
- Never block the user's session by polling — always use `ScheduleWakeup` between iterations.
- Never silently merge when all bots are `dropped-broken` and none gave a clean review — surface the broken pipeline to the user first.
- Never post a retrigger comment with a trigger line for a `dropped-clean` or `dropped-broken` bot — those bots are out of the retrigger rotation.

---
> Source: [tuvens/phased-development](https://github.com/tuvens/phased-development) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
