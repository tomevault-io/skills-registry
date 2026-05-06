---
name: openclaw-customizer
description: > Use when this capability is needed.
metadata:
  author: neversight
---

# OpenClaw Customizer

Help the user configure, customize, and creatively leverage their OpenClaw instance.

## Approach

Be imaginative and inventive. OpenClaw is a flexible system — suggest non-obvious combinations
of features that solve real problems. Think beyond basic chat: cron jobs, multi-agent routing,
hooks, channel-specific personas, memory pipelines, and automation patterns.

When the user asks about a specific area, load the relevant reference file before responding.

## Reference Files

Load these on demand based on what the user needs:

| Topic | File | When to load |
|-------|------|-------------|
| Config schema & settings | [references/configuration.md](references/configuration.md) | Editing openclaw.json, any config question |
| Bootstrap files | [references/bootstrap-files.md](references/bootstrap-files.md) | SOUL.md, USER.md, AGENTS.md, IDENTITY.md, TOOLS.md |
| Channel setup | [references/channels.md](references/channels.md) | Any messaging channel (Telegram, WhatsApp, Discord, Slack, iMessage, etc.) |
| Models & providers | [references/models-providers.md](references/models-providers.md) | Model selection, provider config, failover, auth |
| Tools, skills, hooks, cron | [references/tools-skills-hooks.md](references/tools-skills-hooks.md) | Tool policy, skills, hooks, cron jobs, memory system |
| Multi-agent routing | [references/multi-agent.md](references/multi-agent.md) | Multiple agents, routing, bindings, isolation |
| Creative patterns | [references/creative-patterns.md](references/creative-patterns.md) | Ideas, inspiration, non-obvious uses, advanced patterns |

## Workflow

1. **Understand what the user wants to customize** — ask clarifying questions if needed
2. **Load the relevant reference(s)** — read the specific file(s) for the topic at hand
3. **Propose changes** — show the exact JSON5 config or markdown content to add/modify
4. **Explain the "why"** — help the user understand what each setting does and why you chose it
5. **Suggest adjacent improvements** — if you see an opportunity to make their setup better, mention it

## Key File Locations

- Config: `~/.openclaw/openclaw.json`
- Workspace: `~/.openclaw/workspace/` (or `~/.openclaw/workspace-<agentId>/`)
- Bootstrap: `SOUL.md`, `USER.md`, `AGENTS.md`, `IDENTITY.md`, `TOOLS.md` in workspace root
- Skills: `~/.openclaw/skills/` (shared) or `<workspace>/skills/` (per-agent)
- Hooks: `~/.openclaw/hooks/` (shared) or `<workspace>/hooks/` (per-agent)
- Memory: `<workspace>/memory/YYYY-MM-DD.md` (daily), `<workspace>/MEMORY.md` (long-term)
- Sessions: `~/.openclaw/agents/<agentId>/sessions/`
- Logs: `/tmp/openclaw/openclaw-YYYY-MM-DD.log`
- Cron: `~/.openclaw/cron/jobs.json`

## Live Documentation

For anything not covered in the reference files, fetch from the OpenClaw docs:
- Docs index: `https://docs.openclaw.ai/llms.txt`
- Getting started: `https://docs.openclaw.ai/start/getting-started`
- Configuration: `https://docs.openclaw.ai/gateway/configuration.md`
- Configuration examples: `https://docs.openclaw.ai/gateway/configuration-examples.md`

## Guidelines

- Always show concrete JSON5 snippets or markdown content — not just descriptions
- Use JSON5 format (comments and trailing commas are OK in openclaw.json)
- When suggesting SOUL.md or USER.md content, tailor it to what you know about the user
- Suggest `openclaw doctor` when troubleshooting
- Remind about `allowFrom` security — never suggest `open` DM policy without a warning
- For multi-agent setups, emphasize workspace isolation and credential separation
- When suggesting cron jobs, always include timezone awareness

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
