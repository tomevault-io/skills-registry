---
name: myreload
description: Reload FUTR data after code or content changes Use when this capability is needed.
metadata:
  author: wolfspelz
---

# Reload Data

Trigger a data reload on the local FUTR server after making changes to data files.

## Steps

1. Try to reload data:
```bash
curl -s http://localhost:5000/Reload
```

2. If the reload request fails (connection refused), tell the user to press F5 in VS Code to start the server in debug mode.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wolfspelz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
