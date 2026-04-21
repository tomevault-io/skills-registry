---
name: feed-management-skills
description: | Use when this capability is needed.
metadata:
  author: campbellmcgregor
---

# Feed Management Skills

This skill group manages threat intelligence feeds for NOMAD v2.0.

## Available Commands

| Command | Description | Arguments |
|---------|-------------|-----------|
| `/add-feeds` | Add industry-specific feed packages | `[industry]` |
| `/feed-quality` | Feed performance dashboard | None |
| `/import-feeds` | Import feeds from OPML/JSON/CSV | `[file]` |
| `/refresh` | Force intelligence refresh | None |

## Agent Integration

These commands coordinate with NOMAD agents:
- **feed-manager**: Manages feed subscriptions and imports
- **feed-quality-monitor**: Tracks feed health and performance
- **threat-collector**: Fetches data from configured feeds

## Available Industry Packages

- **healthcare** - Healthcare & Life Sciences feeds
- **financial** - Financial Services & Banking feeds
- **manufacturing** - Manufacturing & Industrial feeds
- **technology** - Technology & Software Development feeds
- **energy** - Energy & Utilities feeds
- **government** - Government & Public Sector feeds

## Trigger Patterns

These commands are auto-suggested when users:
- Mention feeds ("add feeds", "import feeds", "feed quality")
- Ask about data sources ("RSS", "OPML", "threat sources")
- Request data refresh ("refresh", "update data", "stale data")
- Mention industry packages ("healthcare feeds", "financial sources")

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/campbellmcgregor) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
