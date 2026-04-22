---
name: stop-preview
description: Stop a running MkDocs preview server. Use when port 8000 is busy, switching tasks, or done editing documentation. Use when this capability is needed.
metadata:
  author: nextflow-io
---

Stop the MkDocs preview server that is running in the background.

See [../shared/repo-conventions.md](../shared/repo-conventions.md) for directory structure.

This will:

1. Find any running Docker containers with the training-mkdocs image
2. Stop the container(s)
3. Confirm the server has been stopped

If no preview server is running, you'll be notified.

Use the following steps:

1. Run `docker ps --filter "ancestor=ghcr.io/nextflow-io/training-mkdocs:latest" --format "{{.ID}}"` to find running containers
2. If any container IDs are found, stop them with `docker stop <container_id>`
3. Confirm they have been stopped

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nextflow-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
