---
name: voice-status
description: Check the status of the voice assistant — is it in a call, listening, idle? Use when this capability is needed.
metadata:
  author: meetocean-ai
---

# Voice Status

Check the current status of the codebase voice assistant.

## Steps

1. Call `mcp__codebase-voice__get_status` to get:
   - Session state (idle, listening, in_call)
   - Current call info (if in a call)
   - Questions asked this session
   - Audio pipeline health (STT connected, TTS connected)

2. Call `mcp__codebase-voice__get_config` to show configuration status:
   - Which API keys are configured
   - Current wake word
   - Voice settings

3. Display a clear summary to the user.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/meetocean-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
