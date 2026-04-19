---
name: openclaw-install
description: Complete installation and setup guide for OpenClaw, the personal AI assistant. Use this skill when the user wants to install OpenClaw, set up the gateway, configure channels (WhatsApp, Telegram, Slack, Discord, Signal, iMessage), troubleshoot installation issues, update OpenClaw, or understand the system architecture. Also triggers for queries about Clawdbot, Moltbot (previous names), skills management, or ClawdHub registry. Use when this capability is needed.
metadata:
  author: joaopiccioni44-creator
---

# OpenClaw Installation Skill

OpenClaw is a personal AI assistant that runs on your own devices, connecting to messaging channels like WhatsApp, Telegram, Slack, Discord, Signal, iMessage, and more.

## Prerequisites

- **Node.js ≥22** (required)
- **pnpm** (recommended) or npm
- **Git** (for source builds)

Verify Node version:
```bash
node --version  # Must be v22.x or higher
```

## Installation Methods

### Method 1: NPM Global Install (Recommended)

```bash
npm install -g openclaw@latest
# or with pnpm:
pnpm add -g openclaw@latest

# Run onboarding wizard
openclaw onboard --install-daemon
```

The wizard installs the Gateway daemon (launchd on macOS, systemd on Linux) so it stays running.

### Method 2: Build from Source

```bash
git clone https://github.com/openclaw/openclaw.git
cd openclaw

pnpm install
pnpm ui:build      # Auto-installs UI deps on first run
pnpm build

pnpm openclaw onboard --install-daemon

# Dev loop (auto-reload on TS changes)
pnpm gateway:watch
```

### Method 3: Docker

See `references/docker-setup.md` for containerized deployment.

### Method 4: Nix

```bash
# Using nix-clawdbot
nix flake show github:openclaw/nix-clawdbot
```

## Quick Start Commands

```bash
# Start gateway
openclaw gateway --port 18789 --verbose

# Send a message
openclaw message send --to +1234567890 --message "Hello from OpenClaw"

# Talk to the assistant
openclaw agent --message "Ship checklist" --thinking high

# Health check
openclaw doctor

# Update OpenClaw
openclaw update --channel stable
```

## Configuration

Minimal config at `~/.openclaw/openclaw.json`:

```json
{
  "agent": {
    "model": "anthropic/claude-opus-4-5"
  }
}
```

For full configuration options, see `references/configuration.md`.

## Channel Setup

Each channel requires specific configuration. Quick overview:

| Channel | Key Config | Auth Method |
|---------|-----------|-------------|
| WhatsApp | `channels.whatsapp.allowFrom` | QR code via `openclaw channels login` |
| Telegram | `TELEGRAM_BOT_TOKEN` or `channels.telegram.botToken` | BotFather token |
| Slack | `SLACK_BOT_TOKEN` + `SLACK_APP_TOKEN` | Slack App OAuth |
| Discord | `DISCORD_BOT_TOKEN` or `channels.discord.token` | Discord Bot token |
| Signal | `channels.signal` section | signal-cli setup |
| iMessage | macOS only, Messages signed in | Native integration |

For detailed channel setup, see `references/channels.md`.

## Security Defaults

OpenClaw treats inbound DMs as **untrusted input** by default:

- **DM pairing** (`dmPolicy="pairing"`): Unknown senders receive a pairing code
- Approve senders: `openclaw pairing approve <channel> <code>`
- For open access: set `dmPolicy="open"` with `"*"` in allowlist

Run `openclaw doctor` to check for risky configurations.

## Directory Structure

```
~/.openclaw/
├── openclaw.json       # Main configuration
├── credentials/        # Channel credentials (auto-generated)
├── workspace/          # Agent workspace
│   ├── AGENTS.md       # Injected prompt
│   ├── SOUL.md         # Personality config
│   ├── TOOLS.md        # Tool definitions
│   └── skills/         # Local skills
└── state/              # Runtime state
```

## Skills Management

```bash
# Install from ClawdHub
clawhub install <skill-name>

# Sync all skills
clawhub sync --all

# List installed skills
openclaw skills list
```

Skills load from:
1. `<workspace>/skills` (highest priority)
2. `~/.openclaw/skills`
3. Bundled skills (lowest priority)

## Troubleshooting

### Common Issues

**Node version too old:**
```bash
# Install Node 22 via nvm
nvm install 22
nvm use 22
```

**Gateway won't start:**
```bash
openclaw doctor          # Diagnose issues
openclaw gateway --verbose  # See detailed logs
```

**Channel connection fails:**
```bash
openclaw channels status
openclaw channels login    # Re-authenticate
```

**Permission denied (macOS):**
- Grant Terminal/IDE full disk access in System Preferences
- For Voice Wake: grant microphone access

### Logs

```bash
# View gateway logs
tail -f ~/.openclaw/logs/gateway.log

# Debug mode
openclaw gateway --verbose --debug
```

## Development Channels

- **stable**: Tagged releases (`vYYYY.M.D`), npm dist-tag `latest`
- **beta**: Prerelease (`vYYYY.M.D-beta.N`), npm dist-tag `beta`
- **dev**: Moving head of `main`, npm dist-tag `dev`

Switch channels:
```bash
openclaw update --channel stable|beta|dev
```

## Useful Links

- Docs: https://docs.openclaw.ai
- GitHub: https://github.com/openclaw/openclaw
- ClawdHub (Skills): https://clawhub.com
- Discord: https://discord.gg/clawd

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joaopiccioni44-creator) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
