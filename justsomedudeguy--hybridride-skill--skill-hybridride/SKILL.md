---
name: hybridride
description: Manage low-cost model resilience for OpenClaw by ranking OpenRouter free models, configuring primary/fallback model lists, and rotating Gemini API keys from a local key pool when rate-limits or auth issues occur. Use when users ask for free-tier model setup, fallback automation, OpenRouter free model switching, Gemini key rotation, or cost reduction with automatic failover. Use when this capability is needed.
metadata:
  author: justsomedudeguy
---

# HybridRide

Configure OpenClaw for resilient low-cost usage across OpenRouter free models and Gemini key pools.

## Commands

Run via the bundled script:

```bash
python scripts/hybridride.py status
python scripts/hybridride.py auto --fallback-count 6
python scripts/hybridride.py gemini-rotate
python scripts/hybridride.py gemini-set-key --key <GEMINI_API_KEY>
python scripts/hybridride.py gemini-add-key --key <GEMINI_API_KEY>
python scripts/hybridride.py gemini-list-keys
```

## Behavior

- Rank OpenRouter free models from API metadata.
- Set OpenClaw primary model + fallback model list.
- Keep a Gemini key pool in `~/.openclaw/.hybridride-gemini-keys.json`.
- Rotate active `GEMINI_API_KEY` in `~/.openclaw/openclaw.json`.
- Preserve unrelated OpenClaw config fields.

## Notes

- `OPENROUTER_API_KEY` is required for OpenRouter commands.
- Gemini key pool supports round-robin rotation.
- Restart OpenClaw after model/key changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justsomedudeguy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
