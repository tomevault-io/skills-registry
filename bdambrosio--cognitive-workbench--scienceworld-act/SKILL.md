---
name: scienceworld-act
description: Take a ScienceWorld action in the active session. Returns observation, reward, done. No session_id needed - uses active session from executive_node. Use when this capability is needed.
metadata:
  author: bdambrosio
---

# ScienceWorld Act (Level 4)
## Input
- `action`: text command (e.g., "look", "go north", "take shovel")
- No `session_id` needed - uses active session from executive_node.scienceworld_env

## Output
- Note ID (bound to `out` variable) containing:
  - `text`: observation after the action
  - `metadata.reward`, `metadata.done`
  - `metadata.session_id`, `metadata.action`, `metadata.info`

## Workflow
```json
{"type":"scienceworld-reset","out":"$sw"}
{"type":"scienceworld-act","action":"look","out":"$o1"}
{"type":"scienceworld-act","action":"take watering can","out":"$o2"}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdambrosio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
