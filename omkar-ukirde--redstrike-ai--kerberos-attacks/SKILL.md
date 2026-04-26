---
name: kerberos-attacks
description: Skills for Kerberos-based attacks in Active Directory including Kerberoasting and AS-REP Roasting. Use when this capability is needed.
metadata:
  author: omkar-ukirde
---

# Kerberos Attacks

Kerberos protocol exploitation in Active Directory environments.

## Skills

- [Kerberoast](references/kerberoast.md) - Service account TGS ticket cracking
- [AS-REP Roast](references/asreproast.md) - Pre-auth disabled accounts

## Quick Reference

| Attack | Hash Mode | Target |
|--------|-----------|--------|
| Kerberoast | 13100 (RC4), 19700 (AES256) | Service accounts with SPN |
| AS-REP Roast | 18200 | Accounts without preauth |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/omkar-ukirde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
