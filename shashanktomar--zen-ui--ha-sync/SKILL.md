---
name: ha-sync
description: Sync zen-ui plugin to local Home Assistant. Use when user asks to sync, deploy, or push to HA. Use when this capability is needed.
metadata:
  author: shashanktomar
---

# HA Sync

Run from the zen-ui project root:

```bash
just sync-build   # Build and sync
just sync         # Sync only (if already built)
```

After syncing, remind user to hard refresh browser (Ctrl+Shift+R / Cmd+Shift+R).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shashanktomar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
