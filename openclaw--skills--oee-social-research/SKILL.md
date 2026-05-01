---
name: oee-social-research
description: > by Odin's Eye Enterprises — Ancient Wisdom. Modern Intelligence. Use when this capability is needed.
metadata:
  author: openclaw
---
# 🐾 Social Research (Ravens) — What Are People Saying?

> by Odin's Eye Enterprises — Ancient Wisdom. Modern Intelligence.

Tiered X/Twitter research tool. Sends out the ravens, brings back intelligence.

## What It Does

1. **Tier 1** — FxTwitter API (free, fast, public tweets)
2. **Tier 2** — Web search fallback (broader reach)
3. **Tier 3** — Browser scraping (last resort, full fidelity)
4. **Briefings** — Compiled research reports

## Trigger Phrases

- "what are people saying about"
- "social research on"
- "twitter research"
- "send the ravens"
- "what's the buzz on"

## Usage

```bash
# Research a topic
python social_research.py "OpenAI GPT-5 reactions"

# Research with specific tier
python social_research.py "AI agents" --tier 1

# Get cached briefing
python social_research.py --briefing "topic"
```

## Files

- `social_research.py` — main research engine
- `fxtwitter.py` — FxTwitter API client
- `.cache/` — cached results (auto-managed)
- `.briefings/` — compiled reports

## Requirements

- Python 3.10+
- No API keys for Tier 1 (FxTwitter is free)
- Web search available via agent tools for Tier 2

## For Agents

Run from the skill directory:

```bash
python social_research.py "TOPIC"
```

Output is a structured briefing on stdout.

<!-- 🐾 Huginn and Muninn fly at dawn -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
