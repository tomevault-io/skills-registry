---
name: setup
description: This skill should be used when the user asks to "set up headkey", "configure headkey", "connect to headkey", mentions "HEADKEY_API_KEY", "HEADKEY_AGENT_ID", or discusses headkey configuration, authentication setup, or per-project agent configuration. Use when this capability is needed.
metadata:
  author: savantly-net
---

# Headkey Setup Skill

This skill helps users configure their connection to the Headkey Memory API.

## When This Skill Applies

- User wants to set up or configure the Headkey plugin
- User needs help with API key or agent ID configuration
- User is troubleshooting connection issues
- User wants to configure a different Headkey agent per project

## Key Concepts

There are two types of API keys:

- **Account key** (recommended) — a single key for the user's Headkey account. Requires setting a per-project agent ID via `HEADKEY_AGENT_ID` (UUID or friendly slug).
- **Agent key** — a key tied to a specific agent. No agent ID header needed.

Account keys are preferred because you manage one key globally and just set the agent ID per project.

## Configuration

### Account Key (Recommended)

Set the API key **globally** in `~/.claude/settings.json`:

```json
{
  "env": {
    "HEADKEY_API_KEY": "cibfe_your_account_key_here"
  }
}
```

Set the agent ID **per-project** in `.claude/settings.json` in the project root:

```json
{
  "env": {
    "HEADKEY_AGENT_ID": "my-project-agent"
  }
}
```

The agent ID can be the agent UUID or the friendly slug from the agent configuration.

### Agent Key

Set the API key per-project or globally, same as above but only `HEADKEY_API_KEY` is needed — no `HEADKEY_AGENT_ID` required.

**Per-project** — add to `.claude/settings.json` in the project root:

```json
{
  "env": {
    "HEADKEY_API_KEY": "cibfe_your_agent_key_here"
  }
}
```

**Global** — add to `~/.claude/settings.json`:

```json
{
  "env": {
    "HEADKEY_API_KEY": "cibfe_your_agent_key_here"
  }
}
```

### Git Safety

Make sure `.claude/settings.json` is in `.gitignore` to avoid committing secrets. Project-level settings override global settings.

## Setup Command

Users can run `/headkey:setup` for a guided walkthrough that handles key type selection, key configuration, agent ID setup, and `.gitignore` checks.

## Verification

After configuration, restart Claude Code and verify the Headkey MCP tools are available. If the connection fails, check:

1. The API key is valid and not expired
2. Network connectivity to https://www.headkey.ai
3. For account keys: `HEADKEY_AGENT_ID` is set correctly for the current project
4. Project-level settings aren't being overridden unexpectedly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/savantly-net) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
