---
name: telegram-kit
description: >- Use when this capability is needed.
metadata:
  author: radonx
---

# telegram-kit

## Safety invariants (do not violate)

- **Hard split:** `user` = MTProto human account (high-risk). `bot` = Bot API token (lower-risk). Never mix.
- **User API rules:** never run unattended; never schedule; expect login/2FA/code prompts; minimize write actions.
- **Secrets:** do not store credentials inside this skill. Read from `~/.openclaw/.env` and `~/.openclaw/openclaw.json` only.

## Interface (CLI-like subcommands)

- `telegram-kit user …` → read `references/USER.md`
- `telegram-kit bot  …` → read `references/BOT.md`
- End-to-end flow → read `references/FLOWS.md`
- Failures / gotchas → read `references/TROUBLESHOOT.md`

## Implementation notes

- Scripts live in `scripts/`:
  - MTProto (Telethon): `scripts/tg_user.py`
  - Bot API (stdlib urllib): `scripts/tg_bot.py`

When executing scripts, run them directly (do not copy/paste code into chat).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/radonx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
