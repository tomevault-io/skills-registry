---
name: hass-docs
description: Home Assistant documentation and helper Use when this capability is needed.
metadata:
  author: al-one
---

# Home Assistant Developer Docs

## Ask context7
```bash
npx -y ctx7 docs /home-assistant/developers.home-assistant "How to ..."
```

## Ask deepwiki
```bash
npx -y mcporter call mcp.deepwiki.com/mcp.ask_question --timeout 120000 --args '{
  "repoName": "home-assistant/developers.home-assistant",
  "question": "How to ..."
}'
npx -y mcporter call mcp.deepwiki.com/mcp.ask_question --timeout 120000 --args '{
  "repoName": "home-assistant/core",
  "question": "How to ..."
}'
```

## About `mcporter`
To improve compatibility, use `npx -y mcporter` instead of `mcporter` when executing commands.

---
> Source: [al-one/hass-xiaomi-miot](https://github.com/al-one/hass-xiaomi-miot) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
