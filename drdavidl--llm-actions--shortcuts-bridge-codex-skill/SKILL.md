---
name: shortcuts-bridge-codex
description: Trigger allowlisted macOS Shortcuts through a local HTTP bridge from Codex/GPT. Use when the user asks to send messages, create reminders, run personal automations, or execute explicit tasks mapped to macOS Shortcuts. Also use for bridge-backed file saves through the /savefile endpoint. Use when this capability is needed.
metadata:
  author: drdavidl
---

# Shortcuts Bridge for Codex

Use this skill to run macOS Shortcuts through `shortcuts_bridge.py`.
Use the bundled CLI client for all requests:

`shortcuts-bridge-codex-skill/scripts/bridge_client.py`

## Prerequisites

1. Create local config (private, gitignored):

```bash
cp shortcuts_bridge.config.example.json shortcuts_bridge.local.json
```

2. Edit `shortcuts_bridge.local.json` and set:
- `allowed_shortcuts`
- `savefile_path`
- Optional hardening:
  - set `auth_token`
  - keep `allow_remote_clients` as `false`
  - keep `enable_savefile` as `false` unless required
  - keep `cors_allowed_origins` empty unless browser-based calls are required

3. Start the bridge server:

```bash
python3 shortcuts_bridge.py
```

Server URL: `http://127.0.0.1:9876`

## Step 1: Check Bridge Health and Allowed Shortcuts

Run:

```bash
python3 shortcuts-bridge-codex-skill/scripts/bridge_client.py health
```

If `auth_token` is configured, first run:

```bash
export SHORTCUTS_BRIDGE_TOKEN="your-token"
```

Only execute shortcuts listed in `allowed_shortcuts`.

## Step 2: Trigger a Shortcut

Run:

```bash
python3 shortcuts-bridge-codex-skill/scripts/bridge_client.py run \
  --shortcut "SendText" \
  --input "Running 10 minutes late"
```

This calls `POST /run` with `shortcut` and optional `input`.

## Step 3: Save a File (Optional)

Run:

```bash
python3 shortcuts-bridge-codex-skill/scripts/bridge_client.py savefile \
  --filename "report.pdf" \
  --from-file "/absolute/path/to/report.pdf"
```

This calls `POST /savefile` with base64 file data.

## Step 4: Report Result

Success payload contains:

- `success: true`
- `shortcut`
- `output` (if any)

Failure payload contains:

- `success: false`
- `error`
- `allowed` (when shortcut is not allowlisted)

## Troubleshooting

- `Connection refused`: start `shortcuts_bridge.py`.
- `not allowlisted`: add shortcut name to `allowed_shortcuts` in `shortcuts_bridge.local.json`, then restart server.
- timeout/failure: test shortcut manually in Shortcuts.app and verify the shortcut accepts text input when required.
- JSON fields differ from expectation: trust live `/health` output over stale docs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/drdavidl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
