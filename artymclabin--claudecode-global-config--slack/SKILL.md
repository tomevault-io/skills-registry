---
name: slack
description: ALL Slack operations. Use for ANY task involving Slack - messages, channels, members, workspaces, invites, permissions, history, reactions, threads, DMs, bots, apps, settings. If the word "Slack" appears in the request, use this skill. Use when this capability is needed.
metadata:
  author: artymclabin
---

# Slack Messaging

**Purpose:** Send Slack messages via API (channels, DMs, notifications).

**Credential location:** `~/.claude/.env` (gitignored) - contains workspace tokens.

**References:**
- `references/workspace-setup.md` - Workspace configs, domain auto-join, bot capabilities, full scope list

🚨 **API first:** Use Slack API (curl) for ALL messaging operations. Chrome-agent only for admin UI actions (workspace settings, user invites on non-Enterprise plans).

🚨 **Slack URLs → parse and use API, NEVER navigate in browser.** When you encounter a Slack URL, extract the channel and timestamp and use the API:
```
URL: https://{workspace}.slack.com/archives/{CHANNEL_ID}/p{TS_NO_DOT}?thread_ts={THREAD_TS}&cid={CHANNEL_ID}
  → channel = CHANNEL_ID (e.g., CHANNEL_ID_EXAMPLE)
  → ts = THREAD_TS (e.g., 1771393596.062309)
  → API: conversations.replies?channel={CHANNEL_ID}&ts={THREAD_TS}
```
This applies even when URLs come from email, GitHub issues, or other external sources. A Slack URL is a Slack operation, not a browser operation.

🚨 **Blast radius check for app-level changes:** Before modifying ANY Slack app setting at api.slack.com (Socket Mode, Event Subscriptions, scopes, Request URL), MUST read `references/workspace-setup.md § PA Bot App Dependencies` to check all consumers. Changes like enabling Socket Mode silently disable HTTP event delivery, breaking other systems.

## Authentication (Browser)

**🚨 All Slack workspaces use `your-email@gmail.com`** — this is the admin account and the active user across ALL workspaces.

- **Never use** `ceo@your-company.com` or any work domain email for Slack browser login
- Work domain emails may exist as dummy/integration users in some workspaces — they are NOT the admin and NOT the active user
- When chrome-agent needs Slack login, always authenticate with `your-email@gmail.com`

---

## Quick Start

```bash
# 1. Load token from global .env
source ~/.claude/.env

# 2. Choose workspace token
export SLACK_TOKEN="$SLACK_PROJECTB_TOKEN"    # or SLACK_COMPANY_TOKEN, etc.

# 3. Send message
curl -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer ${SLACK_TOKEN}" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data '{"channel":"#channel-name","text":"Your message here"}'
```

---

## API Patterns

### Send to Channel

```bash
curl -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer ${SLACK_TOKEN}" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data '{"channel":"#channel-name","text":"Message text"}'
```

**🚨 After posting, ALWAYS provide message link to user:**
```
https://{workspace}.slack.com/archives/{channel_id}/p{ts_without_dot}
```
- `ts` from response: `1769193392.153219` → remove dot → `p1769193392153219`
- Example: `https://yourcompanyhq.slack.com/archives/CHANNEL_ID_1/p1769193392153219`

### Send DM to User

```bash
curl -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer ${SLACK_TOKEN}" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data '{"channel":"@username","text":"Direct message"}'
```

### Send with Formatting (Blocks)

```bash
curl -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer ${SLACK_TOKEN}" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data '{
    "channel": "#channel-name",
    "blocks": [
      {"type": "header", "text": {"type": "plain_text", "text": "Header"}},
      {"type": "section", "text": {"type": "mrkdwn", "text": "*Bold* and _italic_"}}
    ]
  }'
```

---

## 🚨 Write Operations: Never Retry on Parse Failure

**Slack API POST calls (postMessage, update, delete) are FIRE-AND-FORGET.** The HTTP request sends the message as a side effect. If response parsing fails (Windows pipe issue, JSON error), the message was STILL SENT.

**Mandatory pattern for ALL write operations:**

```bash
# 1. ALWAYS save response to file first
curl -s -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer ${SLACK_TOKEN}" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data "$JSON" -o /tmp/slack_response.json

# 2. Parse the file separately
python -c "import json;f=open(r'$(cygpath -w /tmp/slack_response.json)');r=json.load(f);print('OK' if r.get('ok') else r.get('error','unknown'));print(f'ts={r.get(\"ts\",\"\")}')"
```

**If step 2 fails:** Re-read the file. Do NOT re-run step 1. The message is already sent.

**If step 1 fails (curl error, network timeout):** THEN retry is safe — the request didn't complete.

**Rule:** Parse failure ≠ send failure. Never retry a write operation just because you couldn't read the response.

---

## Error Handling

### `not_in_channel` Error

Bot must be in channel before posting:

```bash
# 1. List channels to get ID
curl -s -X POST "https://slack.com/api/conversations.list" \
  -H "Authorization: Bearer $SLACK_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"types":"public_channel","limit":100}' | grep -B2 "channel-name"

# 2. Join channel
curl -X POST "https://slack.com/api/conversations.join" \
  -H "Authorization: Bearer $SLACK_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"channel":"CHANNEL_ID"}'

# 3. Retry message
```

### `channel_not_found` Error

- For channels: Use `#channel-name` format
- For DMs: Use `@username` or user ID
- Check workspace - token must match workspace where channel exists
- **Private channels:** Bot can't see private channels it's not a member of. Use Chrome to add bot first (see below)

### Private Channel Access

**Problem:** API returns `channel_not_found` for private channels bot isn't in. API can't list them either.

**Solution:** Use Chrome automation to add bot via browser UI (user IS logged in and CAN see the channel):

1. Navigate to Slack workspace in browser
2. Click on the private channel in sidebar
3. Click channel name → Integrations → Add apps
4. Search "PA Bot" → Add
5. Get channel ID from URL: `/client/TEAM_ID/CHANNEL_ID`
6. Now API works for that channel

**🚨 Don't give up:** If Chrome fails once, try again with different approach. Don't fall back to asking user for manual steps.

### `invalid_auth` Error

- Token expired or incorrect
- Check `~/.claude/.env` has correct token for target workspace

---

## JSON Escaping & Dynamic Content

**Important:** Use `--data` with single quotes to avoid shell escaping issues:

```bash
# ✅ Correct - single quotes around JSON
--data '{"channel":"#general","text":"Hello"}'

# ❌ Wrong - double quotes require escaping
-d "{\"channel\":\"#general\",\"text\":\"Hello\"}"
```

**For dynamic content, use printf (NOT heredoc):**

```bash
# ✅ Correct - printf avoids BOM/encoding issues on Windows
JSON=$(printf '{"channel":"%s","text":"%s"}' "#channel-name" "Dynamic: $VARIABLE")
curl -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer ${SLACK_TOKEN}" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data "$JSON"
```

**🚨 AVOID heredocs on Windows** - they introduce BOM characters that appear as `??` in Slack:

```bash
# ❌ Wrong - heredoc adds BOM on Windows (shows "??" prefix in messages)
--data @- <<EOF
{"channel":"#general","text":"Hello"}
EOF
```

**🚨 Special characters get garbled:** Bullets (•), arrows (→), emojis may render as `?` in Slack. Use ASCII alternatives:
- `•` → `-` or `*`
- `→` → `->`
- Or use Slack's built-in emoji syntax: `:arrow_right:`, `:white_check_mark:`

---

## Available Workspaces

Check `~/.claude/.env` for available tokens. Common pattern:
- `SLACK_<WORKSPACE>_TOKEN` - e.g., `SLACK_PROJECTB_TOKEN`, `SLACK_COMPANY_TOKEN`

---

## Common Use Cases

1. **Notify about GitHub issues** - Post link to new/updated issues
2. **System alerts** - Automated notifications from scripts
3. **Team updates** - Broadcast messages to channels
4. **Cross-repo notifications** - Any Claude Code session can send Slack messages

---

## Adding New Workspaces

1. Create Slack App in target workspace (or invite existing PA Bot)
2. Get Bot User OAuth Token (`xoxb-...`)
3. Add to `~/.claude/.env`: `SLACK_<WORKSPACE>_TOKEN=xoxb-...`
4. Ensure bot is invited to required channels

---

## Bot Token Scopes Reference

When creating a new Slack App, add these scopes under OAuth & Permissions → Bot Token Scopes:

| Scope | Purpose |
|-------|---------|
| `chat:write` | Send messages to channels/DMs |
| `channels:read` | List public channels |
| `channels:join` | Join public channels |
| `channels:manage` | Create/archive/rename channels (replaces deprecated `channels:write`) |
| `channels:history` | Read message history in channels bot is member of |
| `users:read` | List users (for DMs) |

**🚨 Note:** `channels:write` no longer exists in Slack API - use `channels:manage` instead.

### User Token Scopes (PA Bot App)

User token (`SLACK_YOURCOMPANY_USER_TOKEN`, `xoxp-`) has these scopes for operations the bot token can't do:

| Scope | Purpose |
|-------|---------|
| `groups:read` | List private channels |
| `groups:history` | Read private channel messages |
| `groups:write` | Invite bot to private channels |

### Auto-Join New Channels

🚨 **Run at start of every `slack-workspace-sweep`.** Ensures bot can see ALL channels before scanning.

```bash
# Invite bot to a private channel using user token
BOT_USER_ID="SLACK_BOT_USER_ID_2"
JSON=$(printf '{"channel":"%s","users":"%s"}' "CHANNEL_ID" "$BOT_USER_ID")
curl -s -X POST "https://slack.com/api/conversations.invite" \
  -H "Authorization: Bearer $SLACK_YOURCOMPANY_USER_TOKEN" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data "$JSON"
```

---

# YourCompany Workflows

**Workspace:** YourCompany (`yourcompanyhq.slack.com`)
**Token:** `SLACK_COMPANY_TOKEN`
**Team ID:** `SLACK_TEAM_ID`

## Channel Reference

| Channel | ID | Purpose | Notes |
|---------|-----|---------|-------|
| `#all-yourcompany` | `CHANNEL_ID_13` | General announcements | Public |
| `#managers` | `CHANNEL_ID_1` | Managers-only discussions | Private |
| `#admins` | `CHANNEL_ID_2` | Admin discussions | Private |
| `#customer-service` | `CHANNEL_ID_3` | Customer service | Private |
| `#sales` | `CHANNEL_ID_4` | Sales | Private |
| `#studio-administration` | `CHANNEL_ID_5` | Studio admin (USER, ADMIN_PERSON, TEAM_MEMBER) | Private |
| `#affiliate` | `CHANNEL_ID_6` | Affiliate program | Private |
| `#hr-intake` | `CHANNEL_ID_7` | HR intake | Private |
| `#bugs` | `CHANNEL_ID_BUGS` | Bug reports from employees | Public |
| `#editing` | `CHANNEL_ID_8` | Content editing | Private |
| `#affiliates` | `CHANNEL_ID_9` | Affiliates | Private |
| `#cs-alerts` | `CHANNEL_ID_10` | CS alerts | Private |
| `#customer-success` | `CHANNEL_ID_11` | Customer success | Private |
| `#marketing` | `CHANNEL_ID_12` | Marketing | Private |
| `#podcast` | `CHANNEL_ID_15` | Podcast | Private |
| `#site-team` | `CHANNEL_ID_14` | Site team (DEVELOPER_A, DEVELOPER_B, ADMIN_PERSON) | Private |
| `#social-media` | `CHANNEL_ID_16` | Social media | Private |
| `#studio-admins` | `CHANNEL_ID_17` | Studio admins | Private |
| `#studio-client-payment-screenshots` | `CHANNEL_ID_18` | Client payment screenshots | Private |
| `#ask-csai` | `CHANNEL_ID_19` | Ask CS AI | Private |
| `#cs-team` | `CHANNEL_ID_20` | CS team internal | Private |
| `#cs_alerts` | `CHANNEL_ID_21` | CS alerts (public) | Public |
| `#test` | `CHANNEL_ID_22` | Test channel | Private |
| `#new-channel` | `CHANNEL_ID_23` | New channel | Public |

🚨 **Bot auto-join:** Bot is now in ALL channels (public + private). When new channels are created, use `conversations.invite` with user token to add bot. See "Auto-Join New Channels" section below.

---

# ProjectA Workflows

**Workspace:** YourBrand (`yourbrand.slack.com`)
**Token:** `SLACK_BRAND_TOKEN`

## Channel Reference

| Channel | ID | Purpose |
|---------|-----|---------|
| `#changelog` | `CHANNEL_ID_24` | Deployment notifications, feature releases |
| `#cf-bugs-qa` | `CHANNEL_ID_25` | Bug reports + QA submissions (unified) |
| `#cf-qa` | `CHANNEL_ID_26` | LEGACY — scan for pending items only, no new posts |

---

## Post Changelog

**When:** After deploying changes to ProjectA (push to master, Vercel deploy)

```bash
source ~/.claude/.env && export SLACK_TOKEN="$SLACK_BRAND_TOKEN"

JSON=$(printf '{"channel":"CHANNEL_ID_24","text":"%s","unfurl_links":false}' "YOUR_CHANGELOG_MESSAGE")

curl -s -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer ${SLACK_TOKEN}" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data "$JSON"
```

**Changelog format:**
```
*ProjectA - New Features* :rocket:

- *Feature Name* - Description
- *Feature Name* - Description

_as of commit <short_hash>_
```

**Finding new features since last changelog:**
```bash
# Get the last reported commit from #changelog, then:
git log <last_commit>..HEAD --oneline | grep "feat:"
```

The commit hash in the message IS the tracking mechanism (SSoT). No separate tracking needed.

---

## Read Bug Reports

**When:** User asks to check bug reports or create GitHub issues from Slack

```bash
source ~/.claude/.env && export SLACK_TOKEN="$SLACK_BRAND_TOKEN"

# CHANNEL_ID_25 = #cf-bugs-qa (unified bugs + QA channel)
curl -s -X POST "https://slack.com/api/conversations.history" \
  -H "Authorization: Bearer ${SLACK_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{"channel":"CHANNEL_ID_25","limit":20}'
```

**Parse response:** Look for messages not from bot (no `bot_id` field) - those are user reports.

---

## Create GitHub Issue from Slack Report

**Workflow:**
1. Read bug reports from Slack
2. Create GitHub issue:

```bash
gh issue create \
  --repo "YourBrand/MyProjectA" \
  --title "Bug: [summary from Slack message]" \
  --body "Reported in Slack #cf-bugs-qa by @username

---
**Original message:**
[Slack message content]

---
*Auto-created from Slack report*"
```

3. Reply in Slack thread:

```bash
source ~/.claude/.env && export SLACK_TOKEN="$SLACK_BRAND_TOKEN"

JSON=$(printf '{"channel":"CHANNEL_ID_25","thread_ts":"%s","text":"Created GitHub issue: %s"}' "MESSAGE_TS" "ISSUE_URL")

curl -s -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer ${SLACK_TOKEN}" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data "$JSON"
```

---

## Notify Issue Resolution

**When:** After closing a GitHub issue that originated from Slack

```bash
source ~/.claude/.env && export SLACK_TOKEN="$SLACK_BRAND_TOKEN"

JSON=$(printf '{"channel":"CHANNEL_ID_25","text":"Resolved: %s\n\n*Fix:* %s\n*Commit:* %s"}' "ISSUE_TITLE" "FIX_SUMMARY" "COMMIT_URL")

curl -s -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer ${SLACK_TOKEN}" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data "$JSON"
```

---

## ProjectA Typical Workflow

```
1. Editor posts bug in #cf-bugs-qa
   ↓
2. User tells Claude: "Check Slack bug reports and create GitHub issues"
   ↓
3. Claude reads #cf-bugs-qa
   ↓
4. Claude creates GitHub issues
   ↓
5. Claude replies in Slack thread with issue link
   ↓
6. User triggers Claude to fix issue (autonomous-issue-dispatch skill)
   ↓
7. Issue resolved, Claude posts to #cf-bugs-qa
   ↓
8. Claude posts changelog to #changelog
```

---

---

# ProjectB Workflows

**Workspace:** ProjectB (`projectb-hq.slack.com`)
**Token:** `SLACK_PROJECTB_TOKEN`
**Team ID:** `SLACK_TEAM_ID_2`

## Channel Reference

| Channel | ID | Purpose | Notes |
|---------|-----|---------|-------|
| `#bugs-general` | `CHANNEL_ID_BUGS_2` | Bug reports from co-founders | Public, pre-existing channel |

## Team

| Name | Slack ID | Role |
|------|----------|------|
| USER | `SLACK_USER_ID_1` | Co-founder |
| COFOUNDER | `SLACK_USER_ID_2` | Co-founder |

---

**Last updated:** 2026-01-29

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artymclabin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
