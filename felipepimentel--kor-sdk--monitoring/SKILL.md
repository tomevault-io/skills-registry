---
name: monitoring
description: How to monitor agent performance and system health Use when this capability is needed.
metadata:
  author: felipepimentel
---

# Monitoring Skill

Use this skill to monitor KOR agent performance and system health.

## Commands

- `/metrics` - View current metrics summary
- `/sessions` - List active sessions

## Tracked Metrics

- **Agent lifecycle**: starts, ends, errors
- **Tool calls**: invocations, latency, success rate
- **Sessions**: duration, message count

## Viewing Metrics

Run the collector script directly:

```bash
python3 ${KOR_PLUGIN_ROOT}/scripts/collector.py summary
```

## Best Practices

1. **Review daily**: Check metrics summary to identify issues
2. **Track errors**: Low success rates indicate problems
3. **Monitor latency**: High tool duration may need optimization

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/felipepimentel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
