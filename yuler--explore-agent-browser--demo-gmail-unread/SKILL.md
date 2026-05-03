---
name: demo-gmail-unread
description: Opens and interacts with Gmail using agent-browser. Use when the user asks to view Gmail, open Gmail, check Gmail unread, or take a screenshot of the Gmail page. Use when this capability is needed.
metadata:
  author: yuler
---

# Gmail Unread

Use this skill when the user asks to view Gmail, open Gmail, check unread mail, or capture the Gmail page. Depends on this project’s browser launch script and agent-browser.

## Prerequisites

- Run from project root (agent-browser and launch script are here).
- For persistent login: try Chrome autofill, click sign-in to attempt auto-login, or pause until the user finishes manual login.

## Flow

### Launch

- Run `./scripts/kill.sh` to ensure no stale browser process.
- Run `./scripts/launch.sh` in the background (long-running; allows manual interaction).
- Wait ~3 seconds for DevTools to be ready.
- Connect using the port from `.env` (e.g. `PORT=9222`): `agent-browser connect $PORT`.

### Operations

- Open Gmail: `agent-browser open https://mail.google.com/`.
- If a login page appears, pause until the user completes sign-in, then continue.
- Click each unread thread, open and review all unread emails.
- Get page text/title: `agent-browser get title`, `agent-browser get text @e1`, etc.
- Write extracted text to `outputs/gmail/<date>/`; `<date>` format: `YYYY-MM-DD` (e.g. `2026-01-02`).
- Save full-page screenshots to `outputs/gmail/<date>/mail-<index>.png`; `<index>` = unread email index.

### Close

- Run `agent-browser close`.

## Command reference

| Purpose              | Command |
|----------------------|---------|
| Connect to browser   | `agent-browser connect 9222` (or `$PORT` from .env) |
| Open Gmail           | `agent-browser open https://mail.google.com/` |
| Full-page screenshot | `agent-browser screenshot -f <path>` |
| Interactive elements | `agent-browser snapshot -i` |
| Click / fill         | Snapshot to get refs (e.g. `@e1`), then `agent-browser click @e1` or `agent-browser fill @e2 "text"` |

For more browser actions, see the agent-browser skill or `agent-browser <command> --help`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yuler) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
