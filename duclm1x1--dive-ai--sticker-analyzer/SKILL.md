---
name: sticker-analyzer
description: Analyze images in media/stickers using Vision API to identify and filter meme/sticker content vs screenshots or documents. Use when this capability is needed.
metadata:
  author: duclm1x1
---

# Sticker Analyzer Skill

Analyzes images in `media/stickers` using Google Gemini Vision API to filter out non-stickers (screenshots, documents).

## Tools

### analyze_stickers
Runs the analysis script.

- No arguments required. Scans `~/.openclaw/media/stickers`.

## Setup
1.  Requires `npm install @google/generative-ai`.
2.  Requires a valid Google AI Studio API Key in `.env` (GEMINI_API_KEY).

## Status
Active. Configured with Gemini 2.5 Flash for high-speed sticker filtering.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
