---
name: volcengine-ai-misc-model-discovery
description: Discover and route Volcengine AI model capabilities. Use when users ask which model to use, need endpoint capability checks, or require model selection matrices. Use when this capability is needed.
metadata:
  author: openclaw
---

# volcengine-ai-misc-model-discovery

Discover available models and map user intent to the most suitable endpoint.

## Execution Checklist

1. Identify task type and constraints (latency, cost, quality).
2. List candidate models/endpoints by capability.
3. Score options and recommend one default plus backups.
4. Output decision table and invocation template.

## Output Schema

- Task type
- Candidate endpoints
- Recommended endpoint
- Tradeoffs and fallback

## References

- `references/sources.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
