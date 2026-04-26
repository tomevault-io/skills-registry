---
name: infrastructure
description: Skills for attacking network infrastructure services including DNS, SNMP, NTP, and RPC. Use when this capability is needed.
metadata:
  author: omkar-ukirde
---

# Network Infrastructure

Core network infrastructure service exploitation.

## Skills

- [DNS Pentesting](references/dns-pentesting.md) - DNS enumeration (53)
- [SNMP Pentesting](references/snmp-pentesting.md) - SNMP information (161/162)
- [NTP Pentesting](references/ntp-pentesting.md) - NTP attacks (123)
- [RPCBind Pentesting](references/rpcbind-pentesting.md) - RPC enumeration (111)
- [NetBIOS Pentesting](references/netbios-pentesting.md) - Windows naming (137-139)
- [MSRPC Pentesting](references/msrpc-pentesting.md) - Microsoft RPC (135)

## Quick Reference

| Service | Port | Key Attack |
|---------|------|------------|
| DNS | 53 | Zone transfer |
| SNMP | 161 | Community strings |
| NTP | 123 | Monlist amplification |
| RPC | 111 | Service enum |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/omkar-ukirde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
