---
name: eightctl
description: Control Eight Sleep smart mattress temperature and settings via eightctl CLI. Use when this capability is needed.
metadata:
  author: kody-w
---

# EightCtl

Control Eight Sleep smart mattress from the command line.

## Setup

```bash
eightctl auth --email user@example.com --password "..."
```

## Get Status

```bash
eightctl status
```

## Set Temperature

```bash
# Set bed temperature (-100 to 100)
eightctl temp set --side left --level 20
eightctl temp set --side right --level -10
```

## Turn On/Off

```bash
eightctl power on --side left
eightctl power off --side right
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
