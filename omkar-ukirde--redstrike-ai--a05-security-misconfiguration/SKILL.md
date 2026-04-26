---
name: a05-security-misconfiguration
description: Skills for exploiting security misconfigurations including XXE, file upload, subdomain takeover, and cache issues per OWASP A05:2021. Use when this capability is needed.
metadata:
  author: omkar-ukirde
---

# Security Misconfiguration (OWASP A05)

Missing or improperly configured security controls at any level of the application stack.

## Skills

- [XXE](references/xxe.md) - XML External Entity injection
- [File Upload](references/file-upload.md) - Unrestricted file upload exploitation
- [Subdomain Takeover](references/subdomain-takeover.md) - Dangling DNS exploitation
- [Cache Deception](references/cache-deception.md) - Web cache poisoning attacks

## Quick Reference

| Attack | Target | Impact |
|--------|--------|--------|
| XXE | XML parsers | File read, SSRF, RCE |
| File Upload | Upload endpoints | Webshell, RCE |
| Subdomain Takeover | Dangling DNS | Phishing, cookies |
| Cache Deception | CDN/proxy | Data theft |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/omkar-ukirde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
