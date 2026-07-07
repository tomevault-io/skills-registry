---
name: eval-ping
description: Eval-only smoke skill: required playbook for any 'ping' message — load with skill.view before replying, do not guess the response. Use when this capability is needed.
metadata:
  author: AtomicBot-ai
---

# eval-ping

This skill exists solely for the eval harness. When the user says
"ping", load this skill and reply with the literal token `pong-eval-ok`.

Procedure:

1. Call `skill.view` with `name: "eval-ping"` to load this body.
2. Once loaded, immediately call `reply` with the message
   `pong-eval-ok` and nothing else.

Never run any other tool while handling a ping.

---
> Source: [AtomicBot-ai/atomic-agent](https://github.com/AtomicBot-ai/atomic-agent) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-07 -->
