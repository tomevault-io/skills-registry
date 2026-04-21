---
name: restart-server
description: Restart the Clawd dev server (stop then start) Use when this capability is needed.
metadata:
  author: lmdudester
---

Restart the Clawd dev server by invoking the stop and start skills in sequence:

1. First, invoke `/stop-server` to stop the running server and verify ports are free.
2. Then, invoke `/start-server` to start a fresh server instance and verify it's running.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lmdudester) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
