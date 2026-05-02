---
name: maintain-clash-scripts
description: Maintain helper scripts under scripts/ for this repo (update_direct_from_cn.py, update_dns.py, update_dns_rules.sh, update_zashboard.sh). Use when creating, modifying, or documenting these scripts, or when wiring their outputs into repo files or cron tasks. Use when this capability is needed.
metadata:
  author: mmm1h
---

# Maintain Clash Scripts

## Scope

- Work within `scripts/` and keep behavior aligned with OpenClash/Mihomo usage.
- Update `scripts/README.md` whenever a script’s behavior, options, or usage changes.

## Script-specific guidance

- Preserve `update_direct_from_cn.py` markers and keep output normalized to `IP-CIDR`/`IP-CIDR6` with `,no-resolve`.
- Keep `update_dns.py` output at `force_ttl_rules.txt` and retain DNS failover + source URL list handling.
- Keep `update_dns_rules.sh` safety checks (download success, non-empty file, header check) and allow user-configurable `REMOTE_URL`/`LOCAL_FILE`/`RESTART_CMD`.
- Keep `update_zashboard.sh` target under `/usr/share/openclash/ui/zashboard`, OpenWrt-friendly download tooling, and quiet-by-default behavior.

## Guardrails

- Avoid changing user-specific URLs, paths, or cron schedules unless asked.
- Keep shell scripts POSIX `sh` compatible (busybox ash friendly).
- Prefer minimal dependencies and clear configuration blocks at the top of scripts.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mmm1h) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
