---
name: 33god-33god-agent-factory
description: Bootstrap new 33GOD ecosystem agents with standardized configuration. Use when creating new agents, spawning workers, or when the user says "spin up an agent", "create a new agent", "deploy a new worker", or needs a new agent for a specific pipeline role. Handles workspace creation, config injection, skill installation, provider mirroring, channel binding, and 33GOD ecosystem onboarding (GOD Docs, Plane, Bloodbank, memory). Use when this capability is needed.
metadata:
  author: delorenj
---

# Agent Factory

Standardized bootstrap for 33GOD ecosystem agents. Every agent ships ready to:

- Consume and produce Bloodbank events
- Read/write GOD Docs
- Track work on Plane boards
- Communicate with Cack (boss) and peer agents
- Use hindsight for long-term memory
- Authenticate against all configured providers

## Quick Start

When the user requests a new agent, gather these parameters:

| Parameter             | Required | Description                                                                                                            |
| --------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------- |
| `id`                  | ✅       | Agent ID (lowercase, no spaces, e.g. `scout`)                                                                          |
| `name`                | ✅       | Display name (e.g. `Scout`)                                                                                            |
| `role`                | ✅       | One of: `manager`, `exec`, or `IC`                                                                                     |
| `purpose`             | ✅       | One-line mission statement                                                                                             |
| `personality`         | ❌       | Vibe/tone (defaults to "competent, concise, team-player")                                                              |
| `channel`             | ❌       | `telegram` (needs bot token)                                                                                           |
| `telegram_bot_token`  | ❌       | If channel=telegram, the BotFather token                                                                               |
| `telegram_account_id` | ❌       | Account ID for multi-bot setup (defaults to agent id)                                                                  |
| `model`               | ❌       | Model override (defaults to ecosystem default)                                                                         |
| `workspace_slug`      | ❌       | Workspace path slug (agent-based default comes from display name first token, e.g. `Momo The Cat` -> `workspace-momo`) |
| `reports_to`          | ❌       | Manager/owner for role charter (default: `Cack`)                                                                       |
| `role_title`          | ❌       | Human-readable charter title (default derived from role)                                                               |
| `directives`          | ❌       | Per-agent directives (repeatable, merged into charter)                                                                 |
| `skills`              | ❌       | Additional skills beyond the base set                                                                                  |

## Bootstrap Procedure

### 1. Create Workspace

```bash
bash /home/delorenj/.openclaw/skills/33god-agent-factory/scripts/bootstrap.sh \
  --id <id> \
  --name <name> \
  --role <role> \
  --purpose "<purpose>" \
  [--personality "<personality>"] \
  [--model "<model>"] \
  [--workspace-slug "<agent-workspace-slug>"] \
  [--reports-to "<manager>"] \
  [--role-title "<charter title>"] \
  [--directive "<agent-specific directive>"]...
```

This creates `~/.openclaw/workspace-<id>/` with all template files populated.

### 2. Role Governance Is Auto-Synced

Bootstrap now calls `scripts/role_governance.py` automatically to:

- upsert this agent in `~/.openclaw/workspace/frameworks/agent-governance/AGENT_ROLE_MATRIX.json`
- apply inherited global prime directives to all managed `AGENTS.md` files
- apply per-agent directives (when provided via `--directive`)

You can re-run sync manually at any time:

```bash
python3 /home/delorenj/.openclaw/skills/33god-agent-factory/scripts/role_governance.py \
  --governance-dir /home/delorenj/.openclaw/workspace/frameworks/agent-governance \
  apply
```

### 3. Update Gateway Config

After running the bootstrap script, update `~/.openclaw/openclaw.json`:

**Add agent to `agents.list`:**

```json
{
  "id": "<id>",
  "name": "<name>",
  "workspace": "/home/delorenj/.openclaw/workspace-<id>",
  "identity": { "name": "<name>" }
}
```

**If Telegram channel, add to `channels.telegram.accounts`:**

```json
"<account_id>": {
  "name": "<name>",
  "dmPolicy": "pairing",
  "botToken": "<token>",
  "groupPolicy": "allowlist",
  "streamMode": "partial"
}
```

**Add binding to `bindings` array:**

```json
{
  "agentId": "<id>",
  "match": {
    "channel": "telegram",
    "accountId": "<account_id>"
  }
}
```

### 4. Restart Gateway

Use the `gateway` tool with `action: "restart"` to pick up the new agent.

### 5. Send Onboarding Briefing

After gateway restart, use `sessions_send` to the new agent's session (`agent:<id>:main`) with an onboarding message that includes:

- Their specific mission and current priorities
- Any immediate tasks from the Plane board
- Context about active projects they'll touch

### 6. Mandatory Process Hardening Check (Required)

Before considering bootstrap complete, verify the new agent understands and acknowledges:

- **PR-only delivery** (no direct merges to `main`/`master`)
- **Ticket branch discipline** (ticket id in branch name)
- **Evidence-first status updates** (`branch`, `last commit`, `git status`, `stash`, `open PRs`)
- **Clean-main handoff** after merges (`git checkout main && git pull --ff-only`)
- **BMAD mandatory** for coding repos (`npx bmad-method@alpha install` when missing)

Use `sessions_send` and require a confirmation response in this structure:

1. Repo hygiene checklist they will run every handoff
2. PR policy acknowledgment
3. BMAD enforcement acknowledgment

## Yi Node Flavors (Role Types)

| Role         | Memory        | Can Delegate | Description                                 |
| ------------ | ------------- | ------------ | ------------------------------------------- |
| `manager`    | ✅ Persistent | ✅ Yes       | Delegators only — coordinate, don't execute |
| `exec`       | ✅ Persistent | ✅ Yes       | Delegator + worker hybrid                   |
| `ic`         | ✅ Persistent | ❌ No        | Individual contributor with full context    |
| `contractor` | ❌ Ephemeral  | ❌ No        | Stateless worker, task-scoped               |

**Invariant:** Delegator ⇒ persistent memory required.

## Base Skills (auto-installed via symlink)

Every agent gets these skills symlinked from Cack's install:

- `33god-creating-and-working-with-projects`
- `33god-service-development`
- `33god-workflow-generator`
- `god-docs`
- `managing-tickets-and-tasks-in-plane`
- `github`
- `ecosystem-patterns`
- `installing-apps-tools-and-services`
- `hindsight`

## Template Files

The bootstrap script generates these from templates in `references/`:

- `AGENTS.md` — Role-aware instructions + **repo execution protocol** (ticket branches, PR-only, evidence-first reporting, clean-main handoff, BMAD mandate)
- `SOUL.md` — Personality + ecosystem identity
- `USER.md` — Jarad's info (static)
- `IDENTITY.md` — Agent identity card
- `TOOLS.md` — Empty, agent fills as needed
- `MEMORY.md` — Pre-seeded with ecosystem context
- `HEARTBEAT.md` — Empty (agent configures as needed)
- `memory/` directory created

## Provider Auth

Providers are configured at the gateway level in `agents.defaults`, so all agents automatically inherit:

- anthropic, github-copilot, openai-codex, opencode, kimi-coding, google, google-antigravity, openrouter

No per-agent provider config needed — it's all in defaults.

## Conventions

- Workspace: `~/.openclaw/workspace-<id>/`
- Session key: `agent:<id>:main`
- Telegram account: matches agent id unless overridden
- All agents know Cack is the coordinator/boss
- All agents use `sessions_send` for inter-agent comms
- Plane workspace: `33god`

## Per-Agent Directive Updates (Post-Bootstrap)

Use this when you want to modify one agent's local directives later:

```bash
python3 /home/delorenj/.openclaw/skills/33god-agent-factory/scripts/role_governance.py \
  --governance-dir /home/delorenj/.openclaw/workspace/frameworks/agent-governance \
  upsert-and-apply \
  --workspace /home/delorenj/.openclaw/workspace-<id> \
  --agent "<name>" \
  --role "<role title>" \
  --reports-to "<manager>" \
  --mission "<mission>" \
  --directive "<local directive 1>" \
  --directive "<local directive 2>"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/delorenj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
