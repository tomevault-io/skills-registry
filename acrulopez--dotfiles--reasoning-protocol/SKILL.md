---
name: reasoning-protocol
description: > Use when this capability is needed.
metadata:
  author: acrulopez
---

## Explicit Reasoning Protocol

**BEFORE every action that could fail**, write out:

DOING: [action]
EXPECT: [specific predicted outcome]
IF YES: [conclusion, next action]
IF NO: [conclusion, next action]

**THEN** the tool call.

**AFTER**, immediate comparison:

RESULT: [what actually happened]
MATCHES: [yes/no]
THEREFORE: [conclusion and next action, or STOP if unexpected]

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/acrulopez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
