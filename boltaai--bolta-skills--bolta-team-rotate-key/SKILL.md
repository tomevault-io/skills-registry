---
name: bolta-team-rotate-key
description: Rotate or revoke an existing API key for a human or agent principal, issuing a replacement key. Use when this capability is needed.
metadata:
  author: boltaai
---

## Steps
1. Check capability: `team:manage_keys`.
2. Call:
   - `bolta.rotate_api_key({workspace_id, api_key_id, action, new_label})`
3. Output result.

## Safety
- If revoke is requested, confirm no automation depends on it (if you can detect), otherwise warn in output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/boltaai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
