---
name: nostr-plantr
description: Specialized skill for Plantr IoT data (Kind 34419 and 4171). Use when this capability is needed.
metadata:
  author: openclaw
---
# nostr-plantr

Specialized skill for Plantr IoT data (Kind 34419 and 4171).

## Dependencies
- Requires core nak commands from: `skills/nostr-nak/SKILL.md`

## Configuration
- **Plant Pot Kind**: `34419`
- **Activity Log Kind**: `4171`

## Usage
Query watering history:
`script -q -c "nak req -k 4171 <relay> -l 20" /dev/null | cat`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
