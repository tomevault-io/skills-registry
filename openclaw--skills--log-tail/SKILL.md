---
name: log-tail
description: Stream recent logs from systemd journal Use when this capability is needed.
metadata:
  author: openclaw
---

# Log Tail

Stream recent logs from the systemd journal. View logs by service unit, control line count, and optionally follow in real time.

## Commands

```bash
# Show recent journal logs (default: 50 lines)
log-tail [--unit <service>] [--lines 50]

# Follow logs for a specific service in real time
log-tail --follow <service>
```

## Install

No installation needed. `journalctl` is always present on systemd-based systems like Bazzite/Fedora.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
