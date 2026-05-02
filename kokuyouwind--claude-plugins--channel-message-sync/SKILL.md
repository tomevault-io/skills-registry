---
name: channel-message-sync
description: Go-style channel message synchronization via filesystem for testing goroutine communication patterns Use when this capability is needed.
metadata:
  author: kokuyouwind
---

This skill provides bash scripts to simulate Go channel communication via filesystem:

- `scripts/send-channel.sh` - Send message to channel
- `scripts/receive-channel.sh` - Receive message from channel (blocking)

Used by code-like-prompt commands (05l, 05m) to demonstrate goroutine-to-goroutine channel communication via filesystem.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kokuyouwind) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
