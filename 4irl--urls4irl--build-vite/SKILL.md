---
name: build-vite
description: Build Vite in docker container and verify the build passes. Use when this capability is needed.
metadata:
  author: 4irl
---

Development occurs inside a Docker container. There is a delay between saving written files, and the files being read by Docker. Account for this.

You MUST check that Vite build occurs appropriately in Docker.
Checking that Vite builds correctly on the host WILL cause a failure.

Use the command: `docker exec u4i-local-vite npm run build 2>&1`

1) The first attempt may have a permissions issue or a slight delay. Wait for the response
2) Otherwise, if it fails on the first attempt, try the command again.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/4irl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
