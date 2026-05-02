---
name: ai-sdk-usage
description: Guidance for using AI SDK UI (ai-sdk-ui) in apps, including chat/completion UIs, streaming UI data, generative UI, message persistence, transport, and error handling. Use when asked how to build or integrate AI SDK UI, hook up streaming UI, or implement UI-level patterns for AI SDK. Use when this capability is needed.
metadata:
  author: bart-jaskulski
---

# AI SDK UI Usage

## Overview

Use this skill to answer questions and implement patterns around AI SDK UI (ai-sdk-ui). Prefer loading only the most relevant reference file(s) instead of all docs.

## Quick workflow

1. Identify the UI task (chatbot, completion UI, streaming UI data, generative UI, persistence, transport, errors, or protocol).
2. Open the matching reference file(s) from `references/`.
3. Follow the documented API and patterns; cross-check related topics (e.g., streaming + transport, errors + resume streams) when needed.

## Reference map

Open only what you need:

- `references/01-overview.md` - high-level AI SDK UI overview
- `references/02-chatbot.md` - chatbot UI setup and basics
- `references/03-chatbot-message-persistence.md` - persist chat messages
- `references/03-chatbot-resume-streams.md` - resume streams
- `references/03-chatbot-tool-usage.md` - tools in chatbot UI
- `references/04-generative-user-interfaces.md` - generative UI patterns
- `references/05-completion.md` - completion UI
- `references/08-object-generation.md` - object generation UI
- `references/20-streaming-data.md` - streaming data to UI
- `references/21-error-handling.md` - UI error handling
- `references/21-transport.md` - transport setup and choices
- `references/24-reading-ui-message-streams.md` - reading UI message streams
- `references/25-message-metadata.md` - message metadata
- `references/50-stream-protocol.md` - stream protocol details
- `references/index.md` - docs index and navigation

## Usage notes

- Keep answers aligned to the documented API names and patterns in the references.
- If a question spans multiple topics, load only the minimum set of references needed.
- If the user requests code, match the framework and runtime shown in the relevant docs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bart-jaskulski) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
