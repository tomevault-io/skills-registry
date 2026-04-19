---
name: voicemode-connect
description: Remote voice via VoiceMode Connect. Use when users want to add voice to Claude Code using their phone or web app, without local STT/TTS setup. Use when this capability is needed.
metadata:
  author: mbailey
---

# VoiceMode Connect

Voice conversations through the voicemode.dev cloud platform. Connect your AI assistant to voice clients (iOS app, web app) without running local STT/TTS services.

## How It Works

**Agents** (Claude Code, claude.ai) connect via MCP to voicemode.dev.
**Clients** (iOS app, web app) connect via WebSocket.
The platform routes voice messages between them.

## Quick Setup

### 1. Add the MCP Server

Add to your Claude Code MCP settings (`~/.claude/settings.json`):

```json
{
  "mcpServers": {
    "voicemode-dev": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://voicemode.dev/mcp"]
    }
  }
}
```

### 2. Authenticate

When you first use a Connect tool, Claude Code will prompt for OAuth authentication. Sign in with your voicemode.dev account.

### 3. Connect a Client

Open the iOS app or web dashboard (voicemode.dev/dashboard) and sign in with the same account.

### 4. Start Talking

Use the `status` tool to see connected devices, then use `converse` to have a voice conversation.

## MCP Tools

| Tool | Description |
|------|-------------|
| `status` | Show connected devices and agents |
| `converse` | Two-way voice conversation via connected client |

## Relationship to Local VoiceMode

| Feature | Local VoiceMode | VoiceMode Connect |
|---------|-----------------|-------------------|
| STT/TTS | Local (Whisper/Kokoro) | Client device (phone/browser) |
| Setup | Install services | Just add MCP server |
| Internet | Optional | Required |
| Latency | Lower | Higher |
| Mobile voice | No | Yes |

**Use both**: Local VoiceMode for desktop voice, Connect for mobile voice.

## Documentation

- [Overview](../../docs/connect/README.md) - What is VoiceMode Connect
- [Architecture](../../docs/connect/architecture.md) - How agents and clients connect
- [Claude Code Setup](../../docs/connect/setup/claude-code.md) - Detailed setup guide
- [MCP Tools Reference](../../docs/connect/reference/mcp-tools.md) - Tool parameters

## Open Questions

- How do multiple agents on the same account interact?
- What happens when multiple clients are connected?
- How is the target device selected for `converse`?

These are documented in [docs/connect/](../../docs/connect/) as we learn more.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mbailey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
