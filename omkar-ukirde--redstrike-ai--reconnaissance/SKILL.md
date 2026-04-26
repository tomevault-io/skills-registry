---
name: reconnaissance
description: Skills for network reconnaissance including scanning, enumeration, and service discovery. Use when this capability is needed.
metadata:
  author: omkar-ukirde
---

# Network Reconnaissance

Initial discovery and enumeration of network services and hosts.

## Skills

- [Network Scanning](references/network-scanning.md) - Host and port discovery
- [Nmap Scan](references/nmap-scan.md) - Detailed nmap usage
- [Banner Grabbing](references/banner-grabbing.md) - Service identification
- [Attack Surface Assessment](references/attack-surface-assessment.md) - Exposure evaluation
- [CVE Lookup](references/cve-lookup.md) - Vulnerability research

## Quick Reference

| Tool | Purpose | Common Flags |
|------|---------|--------------|
| nmap | Port scan | `-sV -sC -p-` |
| masscan | Fast scan | `--rate 10000` |
| nc | Banner grab | `-nv` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/omkar-ukirde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
