---
name: osworld-version
description: Get the API version information from the OSWorld server. Returns API version, server version, and protocol. Use when this capability is needed.
metadata:
  author: bdambrosio
---

# OSWorld Version Tool (Level 4)

## Input
- No parameters required
- `value` parameter is ignored

## Output
- Note ID (bound to `out` variable) containing:
  - `text`: formatted version information
  - `metadata`: raw version data including:
    - `api_version`: API version string
    - `server_version`: server version string
    - `protocol`: protocol string (e.g., "http/json")

## Configuration
- `OSWORLD_URL` environment variable (defaults to `http://localhost:3002`)
- Or pass `osworld_url` in character config's `osworld_config` section

## Common Workflow
```json
{"type":"osworld-version","out":"$version"}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdambrosio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
