---
name: mailgun-simple
description: Send outbound emails via the Mailgun API. Uses MAILGUN_API_KEY, MAILGUN_DOMAIN, MAILGUN_REGION, and MAILGUN_FROM. Use when this capability is needed.
metadata:
  author: openclaw
---

# Mailgun Simple

Send outbound emails using the official Mailgun JS SDK.

## Environment Variables

| Variable | Required | Default | Description |
|---|---|---|---|
| `MAILGUN_API_KEY` | **Yes** | — | Your private Mailgun API key. |
| `MAILGUN_DOMAIN` | **Yes** | `aicommander.dev` | Your verified sending domain. |
| `MAILGUN_REGION` | **Yes** | `EU` | API region: `EU` or `US`. |
| `MAILGUN_FROM` | No | `Postmaster <postmaster@{domain}>` | Default sender address. |

## Setup

```bash
npm install mailgun.js@12.7.0 form-data@4.0.1
```

## Tools

### Send Email
```bash
MAILGUN_API_KEY=xxx MAILGUN_DOMAIN=example.com MAILGUN_REGION=EU node scripts/send_email.js <to> <subject> <text> [from]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
