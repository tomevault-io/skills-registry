---
name: deployment-post-checks
description: Decide deployment success or rollback from smoke-check and optional metrics data. Use when gating final rollout and rollback decisions after deployment. Use when this capability is needed.
metadata:
  author: neversight
---

# Deployment Post Checks

1. Read smoke result JSON.
2. Optionally merge metrics result JSON.
3. Apply thresholds and return success or rollback-required status.

## Command
```bash
python3 skills/deployment-post-checks/scripts/post_check.py --smoke-file smoke.json
python3 skills/deployment-post-checks/scripts/post_check.py --smoke-file smoke.json --metrics-file metrics.json --min-success-rate 99 --max-p95-latency 500
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
