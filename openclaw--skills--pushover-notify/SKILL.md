---
name: pushover-notify
description: Send push notifications to your phone via Pushover (pushover.net). Use when you want reliable out-of-band alerts from OpenClaw: reminders, monitoring alerts, cron/heartbeat summaries, or 'notify me when X happens' workflows. Use when this capability is needed.
metadata:
  author: openclaw
---

# Pushover Notify

Send push notifications through the Pushover API using a small Node script.

## Setup (one-time)

1) Create a Pushover application token and get your user key:
- App token: https://pushover.net/apps/build
- User key: shown on your Pushover dashboard

2) Provide credentials to the runtime (do **not** hardcode into this skill):
- `PUSHOVER_APP_TOKEN`
- `PUSHOVER_USER_KEY`

3) (Optional) set defaults:
- `PUSHOVER_DEVICE` (device name)
- `PUSHOVER_SOUND`

## Send a notification

Use the bundled script:

```bash
PUSHOVER_APP_TOKEN=... PUSHOVER_USER_KEY=... \
  node skills/pushover-notify/scripts/pushover_send.js \
  --title "OpenClaw" \
  --message "Hello from Ted" \
  --priority 0
```

Optional fields:
- `--url "https://..."` and `--url-title "Open"`
- `--sound "pushover"`
- `--device "iphone"`
- `--priority -1|0|1|2`

Emergency priority example:

```bash
PUSHOVER_APP_TOKEN=... PUSHOVER_USER_KEY=... \
  node skills/pushover-notify/scripts/pushover_send.js \
  --title "ALERT" \
  --message "Something is on fire" \
  --priority 2 --retry 60 --expire 3600
```

## Notes

- If you need API field details, read: `references/pushover-api.md`.
- For recurring alerts, prefer `cron` to schedule reminders; the cron job text can instruct Ted to run this skill.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
