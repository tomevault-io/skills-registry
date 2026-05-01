---
name: vizclaw
description: Connect OpenClaw-style runs to VizClaw live rooms from a ClawHub-installable skill. Use when you need quick room creation, JSONL/websocket bridging, trigger and agent event streaming, or safe overview-mode visualization. Use when this capability is needed.
metadata:
  author: openclaw
---

# VizClaw Skill

Use this skill to create a VizClaw room and stream OpenClaw-style events.

## Quick commands

Install from ClawHub:

```bash
npx clawhub@latest install vizclaw
```

Then run your agent normally and VizClaw events auto-stream.

Direct script from vizclaw.com:

```bash
uv run https://vizclaw.com/skills/vizclaw/scripts/connect.py
```

```bash
openclaw run ... --json | uv run https://vizclaw.com/skills/vizclaw/scripts/connect.py --openclaw-jsonl --mode detailed
```

Advanced config (skills, models, reminders, heartbeat):

```bash
uv run https://vizclaw.com/skills/vizclaw/scripts/connect.py \
  --skills "ez-google,ez-unifi,claude-code" \
  --available-models "sonnet,haiku,gpt-4o" \
  --heartbeat-interval 30 \
  --reminders-json '[{"title":"Check email","schedule":"every 30min"}]'
```

## Safety

- In `overview`/`hidden` mode, query/tool/report text is redacted.
- Do not stream secrets or sensitive data you are not allowed to share.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
