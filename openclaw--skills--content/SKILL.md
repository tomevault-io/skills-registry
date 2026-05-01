---
name: xmtp-cli-content
description: Demonstrate XMTP content types from the CLI. Use when sending text, markdown, attachment, transaction, deeplink, or miniapp content. Use when this capability is needed.
metadata:
  author: openclaw
---

# CLI content

Demonstrate various XMTP content types: text (with reply/reaction), markdown, attachment, transaction, deeplink, miniapp.

## When to apply

- Sending or testing text, markdown, or attachment content
- Sending or testing transaction, deeplink, or miniapp content

## Rules

- `content-types` – `content text` / `markdown` / `attachment` / `transaction` / `deeplink` / `miniapp` and options

## Quick start

```bash
xmtp content text --target 0x1234...
xmtp content markdown --target 0x1234...
xmtp content attachment --target 0x1234...
xmtp content transaction --target 0x1234... --amount 0.1
```

Read `rules/content-types.md` for details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
