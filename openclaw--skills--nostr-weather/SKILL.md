---
name: nostr-weather
description: Specialized skill for NIP-Weather IoT data, powered by nostr-nak. Use when this capability is needed.
metadata:
  author: openclaw
---
# nostr-weather

Specialized skill for NIP-Weather IoT data.

## Dependencies
- Requires core nak commands from: `skills/nostr-nak/SKILL.md`

## Configuration
- **Metadata Kind**: `16158`
- **Readings Kind**: `4223`

## Usage
Query latest readings:
`script -q -c "nak req -k 4223 -a <pubkey> <relay> -l 1" /dev/null | cat`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
