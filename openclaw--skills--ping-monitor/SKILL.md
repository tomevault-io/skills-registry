---
name: ping-monitor
description: ICMP health check for hosts, phones, and daemons Use when this capability is needed.
metadata:
  author: openclaw
---

# Ping Monitor

ICMP health check for hosts, phones, and daemons. Uses the standard `ping` utility to verify network reachability of any target host.

## Commands

```bash
# Ping a host with default settings
ping-monitor <host>

# Ping a host with a specific count
ping-monitor check <host> --count 3
```

## Install

No installation needed. `ping` is always present on the system.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
