---
name: docker-ctl
description: Inspect containers, logs, and images via podman Use when this capability is needed.
metadata:
  author: duclm1x1
---

# Docker Ctl

Inspect containers, logs, and images via podman. On Bazzite/Fedora, podman is the default container runtime and is always available.

## Commands

```bash
# List running containers
docker-ctl ps

# View container logs
docker-ctl logs <container>

# List local images
docker-ctl images

# Inspect a container
docker-ctl inspect <container>
```

## Install

No installation needed. Bazzite uses `podman` as its container runtime and it is pre-installed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
