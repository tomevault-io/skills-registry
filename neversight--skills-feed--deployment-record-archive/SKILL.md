---
name: deployment-record-archive
description: Archive deployment records with merged common+environment config context (including remote port) for Makefile-first deployment workflow. Use when this capability is needed.
metadata:
  author: neversight
---

# Deployment Record Archive

1. Read `.deploy.env.common` and `.deploy.env.<ENV_MODE>`.
2. Merge context and archive deployment result into JSONL.
3. Return record ID and archive location.

## Command
```bash
python3 skills/deployment-record-archive/scripts/archive_record.py \
  --root . \
  --env-mode prod \
  --version v2026.02.10.1 \
  --actor ci-bot \
  --result success \
  --archive-file deployment-records.jsonl
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
