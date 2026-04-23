---
name: ask
description: Single-model consultation using consultant agent. Defaults to gpt-5.2-pro. Use when this capability is needed.
metadata:
  author: doodledood
---

Consult an external model about: $ARGUMENTS

---

Use the Task tool with `subagent_type='consultant:consultant'`. Pass the question/topic above as the consultant prompt.

**Defaults**:
- Model: `gpt-5.2-pro` (unless user specifies another, e.g., "use claude-opus-4-5-20251101 to...")
- Single-model mode

The agent handles context gathering, CLI invocation, and response relay.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/doodledood) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
