---
name: geo-ip
description: Look up geographic location for any IP address Use when this capability is needed.
metadata:
  author: openclaw
---

# Geo IP

Look up the geographic location for any IP address using the ipinfo.io API. Returns city, region, country, coordinates, and ISP information.

## Commands

```bash
# Look up location for a specific IP address
geo-ip <ip-address>

# Look up your own public IP location
geo-ip me
```

## Install

No installation needed. `curl` is always present on the system. Uses the public ipinfo.io API.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
