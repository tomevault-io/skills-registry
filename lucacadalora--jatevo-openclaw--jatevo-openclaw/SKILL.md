---
name: jatevo
description: Free inference-as-a-service via Jatevo.id — access Qwen 3.5 Plus, Kimi K2.5, and GLM 4.7 at zero cost. Use when you want free, high-quality LLM models for OpenClaw. Use when this capability is needed.
metadata:
  author: lucacadalora
---

# Jatevo — Free LLM Inference for OpenClaw

[Jatevo.id](https://jatevo.id) provides **free inference-as-a-service** for open-source LLMs. Zero cost, production-grade, hosted in Asia with international endpoints.

## Why Jatevo?

- **$0 cost** — All models are completely free
- **3 flagship models** — Qwen 3.5 Plus, Kimi K2.5, GLM 4.7
- **Blazing fast** — GLM 4.7 at 448 tokens/sec, Qwen 3.5+ at 50 tok/s
- **OpenAI-compatible** — Drop-in replacement, works with any OpenAI SDK
- **1M token context** — Qwen 3.5 Plus supports up to 1 million tokens
- **Production-grade** — 32 concurrent requests tested, fully parallel

## Quick Setup (30 seconds)

### Option 1: Run the setup script (recommended)

```bash
bash setup.sh
```

This will:
1. Ask for your Jatevo API key (get one free at [jatevo.id](https://jatevo.id))
2. Patch your OpenClaw config with all available models
3. Optionally set Jatevo as your default model
4. Test the connection

### Option 2: Manual setup

1. **Get your API key** at [jatevo.id](https://jatevo.id) — sign up and generate a key (starts with `jk_`)

2. **Add to your OpenClaw config** (`~/.openclaw/openclaw.json`):

Add this under `models.providers`:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "jatevo": {
        "baseUrl": "https://jatevo.id/api/open/v1/inference",
        "apiKey": "jk_your_key_here",
        "api": "openai-completions",
        "models": [
          {
            "id": "qwen3.5-plus",
            "name": "Qwen 3.5 Plus",
            "reasoning": true,
            "input": ["text", "image"],
            "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
            "contextWindow": 1000000,
            "maxTokens": 65536
          },
          {
            "id": "kimi-k2.5",
            "name": "Kimi K2.5",
            "reasoning": true,
            "input": ["text", "image"],
            "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
            "contextWindow": 262144,
            "maxTokens": 32768
          },
          {
            "id": "glm-4.7",
            "name": "GLM 4.7",
            "reasoning": true,
            "input": ["text"],
            "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
            "contextWindow": 128000,
            "maxTokens": 40000
          }
        ]
      }
    }
  }
}
```

3. **Set your API key** as an environment variable:

```bash
## Replace "jk_your_key_here" above with your actual Jatevo API key
## The key is embedded directly in openclaw.json — no .env needed
```

4. **Set as default model** (optional):

```json
{
  "agents": {
    "defaults": {
      "model": { "primary": "jatevo/qwen3.5-plus" }
    }
  }
}
```

5. **Restart the gateway:**

```bash
openclaw gateway restart
```

## Available Models

### Qwen 3.5 Plus — `jatevo/qwen3.5-plus`

The best all-rounder. Handles everything from general conversation to complex analysis.

| Spec | Value |
|------|-------|
| Context Window | **1,000,000 tokens** (1M) |
| Max Output | 65,536 tokens |
| Speed | **~50 tokens/sec** |
| Input Types | Text + Image (vision) |
| Reasoning | ✅ Yes |
| Best For | General purpose, long documents, vision tasks, daily driver |

**Performance**: Benchmarked at ~95% of Claude Opus 4.6 quality. Handles 1M context effortlessly — feed it entire codebases, books, or document collections. Vision capability lets it analyze images, charts, and screenshots.

**Speed breakdown** (tested March 2026):
- Short prompts: 38 tok/s
- Code generation: 65 tok/s
- Long-form writing: 41 tok/s
- Average across tasks: **50.7 tok/s**

---

### Kimi K2.5 — `jatevo/kimi-k2.5`

Moonshot AI's reasoning powerhouse. Excels at step-by-step logic and complex problem solving.

| Spec | Value |
|------|-------|
| Context Window | **262,144 tokens** (262K) |
| Max Output | 32,768 tokens |
| Speed | **~40 tokens/sec** |
| Input Types | Text + Image (vision) |
| Reasoning | ✅ Yes (strong) |
| Best For | Reasoning, math, coding, structured analysis |

**Performance**: Kimi K2.5 shines on tasks requiring chain-of-thought reasoning. Particularly strong at math, logic puzzles, and multi-step coding problems. Vision support for analyzing diagrams and technical drawings.

**Speed breakdown** (tested March 2026):
- Short prompts: 29 tok/s
- Code generation: 42 tok/s
- Long-form writing: 41 tok/s
- Average across tasks: **39.5 tok/s**

---

### GLM 4.7 — `jatevo/glm-4.7`

The speed demon. Zhipu AI's GLM family delivers the fastest inference of any model in this tier.

| Spec | Value |
|------|-------|
| Context Window | **128,000 tokens** (128K) |
| Max Output | 40,000 tokens |
| Speed | **~448 tokens/sec** 🔥 |
| Input Types | Text |
| Reasoning | ✅ Yes |
| Best For | Fast responses, bilingual (EN/ZH), batch processing, real-time apps |

**Performance**: GLM 4.7 is **9x faster** than Qwen 3.5 Plus and **11x faster** than Kimi K2.5. Best bilingual model for Chinese and English content. Ideal for latency-sensitive applications, chatbots, and high-throughput batch processing.

**Speed breakdown** (tested March 2026):
- Short prompts: 327 tok/s
- Code generation: 672 tok/s 🔥
- Long-form writing: 375 tok/s
- Average across tasks: **448.3 tok/s**

## Model Comparison

| | Qwen 3.5 Plus | Kimi K2.5 | GLM 4.7 |
|---|---|---|---|
| **Speed** | 50 tok/s | 40 tok/s | **448 tok/s** 🔥 |
| **Context** | **1M tokens** | 262K | 128K |
| **Max Output** | **65K** | 32K | 40K |
| **Vision** | ✅ | ✅ | ❌ |
| **Quality** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐½ | ⭐⭐⭐⭐ |
| **Reasoning** | Strong | **Strongest** | Good |
| **Speed Rank** | 3rd | 2nd* | **1st** |
| **Cost** | Free | Free | Free |

### When to Use Which

- **"I need the smartest model"** → `jatevo/qwen3.5-plus`
- **"I need deep reasoning"** → `jatevo/kimi-k2.5`
- **"I need it NOW"** → `jatevo/glm-4.7` (448 tok/s is near-instant)
- **"I have a huge document"** → `jatevo/qwen3.5-plus` (1M context)
- **"I need to analyze an image"** → `jatevo/qwen3.5-plus` or `jatevo/kimi-k2.5`
- **"I'm building a chatbot"** → `jatevo/glm-4.7` (speed matters for UX)

## Aliases

Add these to your OpenClaw config for quick model switching:

```json
{
  "agents": {
    "defaults": {
      "models": {
        "jatevo/qwen3.5-plus": { "alias": "qwen" },
        "jatevo/kimi-k2.5": { "alias": "kimi" },
        "jatevo/glm-4.7": { "alias": "glm" }
      }
    }
  }
}
```

Then switch models with `/model qwen`, `/model kimi`, or `/model glm`.

## Rate Limits

- ~1,200 prompts per 5 hours (~4 req/min sustained)
- Fully parallel — tested 32 concurrent requests successfully
- No additional rate limits from Jatevo

## Troubleshooting

- **"Invalid API key"** → Make sure your key starts with `jk_`
- **Rate limited** → Wait a few minutes, the window resets on a rolling basis
- **Model not found** → Run `openclaw models list` to verify the provider loaded
- **Gateway didn't pick up changes** → Run `openclaw gateway restart`

## Support

- Website: [jatevo.id](https://jatevo.id)
- Questions: Open an issue or ask in the [OpenClaw Discord](https://discord.com/invite/clawd)

---
> Source: [lucacadalora/jatevo-openclaw](https://github.com/lucacadalora/jatevo-openclaw) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
