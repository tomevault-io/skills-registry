---
name: discord-local-stt-tts-installer
description: (macOS) Discord voice assistant installer. Install/update discord-local-stt-tts (Discord voice, Discord local, local STT + local TTS) from GitHub Releases. Use when this capability is needed.
metadata:
  author: openclaw
---

# discord-local-stt-tts-installer (macOS)

ClawHub skill that installs/updates the **discord-local-stt-tts** OpenClaw plugin.

- Plugin repo: https://github.com/vilmire/discord-local-stt-tts
- Install path: `~/.openclaw/openclaw-extensions/plugins/discord-local-stt-tts`

## Supported platforms
- **macOS only**

## Requirements
- `curl`
- `python3` (used by the installer; also needed for some local STT engines)
- `unzip`
- `ffmpeg` (required by the plugin runtime)
- Optional: `pnpm` (if you want the installer to attempt a build)

## macOS permissions
If you use Apple Speech (`apple-speech`) local STT, macOS may require:
- System Settings → Privacy & Security → **Speech Recognition**
- System Settings → Privacy & Security → **Microphone**

## Install / Update
```bash
bash bin/install.sh
openclaw gateway restart
```

## What the installer does
1) Downloads the **latest GitHub Release** source zipball
2) Backs up any existing plugin folder
3) Installs into the OpenClaw extensions plugin directory
4) If `pnpm` is available, attempts `pnpm i && pnpm build` (best-effort)

## Notes
- This skill does **not** modify your `openclaw.json`. You still need to enable/configure the plugin.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
