---
name: clawprompt
description: >- Use when this capability is needed.
metadata:
  author: openclaw
---

# ClawPrompt 🦞📝 — Smart Teleprompter with Mobile Remote

## What It Does
A browser-based teleprompter that runs on your Mac. A second person can use their phone as a remote control to turn pages while the speaker focuses on the camera.

## Quick Start

```bash
cd {SKILL_DIR}/scripts
npm install --silent
node server.js
```

Then open `http://localhost:7870` on the computer.

## How It Works
1. **Computer**: Open the teleprompter page → paste or type your script → click "开始提词"
2. **Phone**: Scan the QR code shown on the computer → phone becomes a remote controller
3. **Recording**: Speaker looks at camera, peripheral vision reads text at top of screen. Another person holds the phone and taps "下一句" to advance.

## Controls
- **Computer keyboard**: Space/↓ = next, ↑ = prev, +/- = font size, ESC = exit
- **Phone**: Tap "下一句" / "上一句" buttons
- **Text upload**: From either computer or phone

## Integration with ClawCut
If using ClawCut to generate video scripts, the 9-scene script can be pasted directly into ClawPrompt.

## Requirements
- Node.js (for WebSocket server)
- Computer and phone on the same WiFi network
- Port 7870 (configurable via PORT env var)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
