---
name: llm-supervisor-agent
description: Automatically switches OpenClaw between cloud and local Ollama models when rate limits occur. Use when this capability is needed.
metadata:
  author: openclaw
---
# LLM Supervisor

Automatically switches OpenClaw between cloud and local Ollama models when rate limits occur.

## Features

- Detects rate-limit / overload errors from Anthropic/OpenAI
- Switches to a local Ollama fallback model
- Requires explicit confirmation before local code generation
- Supports manual commands:

### Commands

- `/llm status`
- `/llm switch cloud`
- `/llm switch local`

## Default Local Model

- `qwen2.5:7b`

## Safety

Local code generation requires the user to type:

`CONFIRM LOCAL CODE`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
