---
name: volcengine-compute-function
description: Build and operate Volcengine Function Compute workloads. Use when users need function deployment, event triggers, runtime configuration, or serverless troubleshooting. Use when this capability is needed.
metadata:
  author: openclaw
---

# volcengine-compute-function

Deploy and troubleshoot serverless functions with predictable packaging and runtime checks.

## Execution Checklist

1. Confirm runtime, trigger type, and region.
2. Build package and validate environment variables.
3. Deploy function and verify latest revision.
4. Invoke test event and return logs/latency summary.

## Reliability Rules

- Keep deployment artifacts versioned.
- Separate config from code.
- Include rollback target in outputs.

## References

- `references/sources.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
