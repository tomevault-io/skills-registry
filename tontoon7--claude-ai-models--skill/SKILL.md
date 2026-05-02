---
name: ai-models
description: Fetch latest AI model IDs from Anthropic, OpenAI, and Google APIs to use accurate model names in code Use when this capability is needed.
metadata:
  author: tontoon7
---

## When to use

Use this skill **automatically** whenever you write code that calls AI APIs and need to specify a model ID.

**Triggers:**
- Writing API calls to Anthropic, OpenAI, or Google AI
- Setting `model` parameters in any AI SDK (Vercel AI SDK, LangChain, etc.)
- User asks about latest available models
- Any code involving `model:` or `model=` for AI providers

## How to use

Run the fetch script — it scrapes public sources, no API keys needed:

```bash
bash ~/.claude/skills/ai-models/fetch-models.sh
```

Options:
- `--all` (default): All providers
- `--anthropic`: Anthropic only
- `--openai`: OpenAI only
- `--google`: Google Gemini only

**Sources:**
- Anthropic: official docs page (docs.anthropic.com)
- OpenAI: SDK source of truth on GitHub (auto-generated from OpenAPI spec)
- Google: official Gemini docs (ai.google.dev)

## Rules

1. **Always run the script** before using any model ID in code — never guess or use memorized IDs
2. **Use the exact model ID** from the script output
3. **Pick the right tier** for the use case:
   - **Flagship** (Opus / GPT-4o / Gemini Pro): Complex reasoning, high quality
   - **Balanced** (Sonnet / GPT-4o-mini / Gemini Flash): Best speed/quality ratio, default choice
   - **Fast** (Haiku / GPT-3.5 / Gemini Flash Lite): Speed-first, simple tasks, cost-effective
4. **Prefer aliases** (e.g. `claude-sonnet-4-5`) over dated versions unless pinning is needed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tontoon7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
