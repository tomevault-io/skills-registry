---
name: agent-council
description: Collect and synthesize opinions from multiple AI agents. Use when users say "summon the council", "ask other AIs", or want multiple AI perspectives on a question. Use when this capability is needed.
metadata:
  author: team-attention
---

# Agent Council

Collect multiple AI opinions and synthesize one answer.

## Usage

Run a job and collect results:

```bash
JOB_DIR=$(./skills/agent-council/scripts/council.sh start "your question here")
./skills/agent-council/scripts/council.sh wait "$JOB_DIR"
./skills/agent-council/scripts/council.sh results "$JOB_DIR"
./skills/agent-council/scripts/council.sh clean "$JOB_DIR"
```

One-shot:

```bash
./skills/agent-council/scripts/council.sh "your question here"
```

## References

- `references/overview.md` — workflow and background.
- `references/examples.md` — usage examples.
- `references/config.md` — member configuration.
- `references/requirements.md` — dependencies and CLI checks.
- `references/host-ui.md` — host UI checklist guidance.
- `references/safety.md` — safety notes.

---
> Source: [team-attention/agent-council](https://github.com/team-attention/agent-council) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
