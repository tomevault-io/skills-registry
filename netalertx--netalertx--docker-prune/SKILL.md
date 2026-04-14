---
name: netalertx-docker-prune
description: Clean up unused Docker resources. Use this when asked to prune docker, clean docker, remove unused images, free disk space, or docker cleanup. DANGEROUS operation. Requires human confirmation. Use when this capability is needed.
metadata:
  author: netalertx
---

# Docker Prune

**DANGER:** This destroys containers, images, volumes, and networks. Any stopped container will be wiped and data will be lost.

## Command

```bash
/workspaces/NetAlertX/.devcontainer/scripts/confirm-docker-prune.sh
```

## What Gets Deleted

- All stopped containers
- All unused images
- All unused volumes
- All unused networks

## When to Use

- Disk space is low
- Build cache is corrupted
- Clean slate needed for testing
- After many image rebuilds

## Safety

The script requires explicit confirmation before proceeding.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/netalertx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
