---
name: bolta-team-create-agent-teammate
description: Create an agent identity (service account) inside a workspace and issue an API key with least privilege. Use when this capability is needed.
metadata:
  author: boltaai
---

## Rules
- Default role is Creator.
- Never create an Admin agent unless explicitly requested.

## Steps
1. Verify capability includes `team:manage` (or equivalent).
2. Create agent principal:
   - `bolta.create_agent_principal({workspace_id, name: agent_name, role, key_label})`
3. Return agent_id, role, key preview, allowed actions.

## Output
Return created agent identity + capabilities summary.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/boltaai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
