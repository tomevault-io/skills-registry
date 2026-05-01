---
name: lunara-voice
description: Bundle for Lunara Voice OpenClaw plugin with install and publish helpers Use when this capability is needed.
metadata:
  author: openclaw
---

# Lunara Voice Bundle

This skill bundle ships everything needed to install and run the Lunara Voice plugin locally.

## What is included

- `plugin/` — full copy of the OpenClaw plugin source
- `scripts/install-plugin.sh` — local install helper
- `references/PUBLISH.md` — release and ClawHub publish steps

## Install plugin from this bundle

Run:

```bash
bash {baseDir}/scripts/install-plugin.sh
```

Then restart Gateway.

## Configure plugin

Set plugin config in `~/.openclaw/openclaw.json`:

```json
{
  "plugins": {
    "entries": {
      "lunara-voice": {
        "enabled": true,
        "config": {
          "apiBaseUrl": "https://your-server.herokuapp.com",
          "apiKey": "lnr_live_KEYID_SECRET",
          "userEmail": "user@example.com"
        }
      }
    }
  }
}
```

## Verify

```bash
openclaw plugins list
openclaw plugins info lunara-voice
openclaw plugins doctor
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
