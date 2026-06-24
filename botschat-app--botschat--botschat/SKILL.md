---
name: botschat
description: Chat with your AI agents via BotsChat — manage channels, sessions, tasks, and messages with E2E encryption Use when this capability is needed.
metadata:
  author: botschat-app
---

# BotsChat CLI Skill

Interact with [BotsChat](https://botschat.app) directly from OpenClaw. Send messages, manage channels, check task status, and view job history — all with end-to-end encryption.

## Install

```bash
npx skills add botschat-app/botsChat
```

Or use directly with npx (no install):

```bash
npx @botschat/botschat login
npx @botschat/botschat chat "Hello"
```

## Setup

```bash
npx @botschat/botschat login
```

Opens your browser for OAuth login (Google/GitHub/Apple). Credentials are saved to `~/.botschat/config.json`.

## Commands

- `npx @botschat/botschat chat "message"` — Send a message and get a response
- `npx @botschat/botschat chat -i` — Interactive chat mode
- `npx @botschat/botschat channels` — List channels
- `npx @botschat/botschat sessions <channelId>` — List sessions
- `npx @botschat/botschat tasks` — List background tasks
- `npx @botschat/botschat tasks run <channelId> <taskId>` — Run a task
- `npx @botschat/botschat jobs <taskId>` — View job history
- `npx @botschat/botschat messages <sessionKey>` — View message history
- `npx @botschat/botschat models` — List available models
- `npx @botschat/botschat status` — Check OpenClaw connection status
- `npx @botschat/botschat config e2e --password <pwd>` — Set E2E encryption password

Use `--json` for machine-readable output: `npx @botschat/botschat --json channels`

---
> Source: [botschat-app/botsChat](https://github.com/botschat-app/botsChat) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
