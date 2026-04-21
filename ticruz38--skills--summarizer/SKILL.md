---
name: summarizer
description: Summarize long text using AI Use when this capability is needed.
metadata:
  author: ticruz38
---

# Summarizer Skill

Summarize long text into short summaries.

## Capabilities

- `summarize <text> [maxLength]` - Summarize text
  - Returns: `{ "summary": "...", "originalLength": 1234, "summaryLength": 150 }`
  - Example: `summarize.js "Long text here..." 100`

## Setup

No setup required! Works immediately.

Optional: Set `OPENAI_API_KEY` for better summaries using GPT-4.

## Examples

```bash
# Basic summarization (local algorithm)
./summarize.js "This is a very long article about something interesting..."

# With custom length
./summarize.js "Long text..." 200

# With OpenAI for better quality
OPENAI_API_KEY=sk-xxx ./summarize.js "Long text..."
```

## Note

This is a simple example skill with no onboarding questions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ticruz38) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
