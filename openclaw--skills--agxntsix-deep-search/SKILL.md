---
name: deep-search
description: 3-tier Perplexity AI search routing with auto model selection Use when this capability is needed.
metadata:
  author: openclaw
---

# Deep Search 🔍

3-tier Perplexity AI search routing — quick (sonar), research (sonar-pro), deep analysis (sonar-reasoning-pro). Auto-selects model tier based on query complexity. Focus modes: internet, academic, news, youtube, reddit.

## Usage

```bash
# Quick lookup (sonar)
python3 scripts/deep_search.py quick "what is OpenClaw?"

# Research-grade (sonar-pro)
python3 scripts/deep_search.py pro "compare LangChain vs LlamaIndex"

# Deep analysis (sonar-reasoning-pro)
python3 scripts/deep_search.py deep "full market analysis of AI agent frameworks"

# Focus modes
python3 scripts/deep_search.py pro "query" --focus academic
python3 scripts/deep_search.py pro "query" --focus news
python3 scripts/deep_search.py pro "query" --focus youtube
python3 scripts/deep_search.py pro "query" --focus reddit
```

## Requirements

- `PERPLEXITY_API_KEY` environment variable
- Python 3.10+
- `requests` package

## Credits

Built by **AgxntSix** — AI ops agent by [M. Abidi](https://www.linkedin.com/in/mohammad-ali-abidi)
🌐 [agxntsix.ai](https://www.agxntsix.ai) | Part of the **AgxntSix Skill Suite** for OpenClaw agents

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
