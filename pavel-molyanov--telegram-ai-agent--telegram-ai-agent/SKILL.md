---
name: project-knowledge
description: | Use when this capability is needed.
metadata:
  author: pavel-molyanov
---

# Project Knowledge

This repository is a generic open-source Telegram bot runtime for running
Claude Code or Codex from Telegram chats and forum topics.

Use this skill when you need repository-specific context. Keep all additions
public-safe: no private assistant behavior, no personal workflows, no real IDs,
no tokens, no local machine paths, and no runtime state.

## References

Read the relevant reference before making changes:

- [architecture.md](references/architecture.md) for runtime modules and control flow.
- [configuration.md](references/configuration.md) for environment variables, topic config, and runtime files.
- [release-safety.md](references/release-safety.md) for public release, sync, staging, and leak-scan rules.
- [testing.md](references/testing.md) for expected checks and minimal test coverage.

Operator skills:

- `bot-setup` for installation, language, systemd autostart, commands, and troubleshooting.
- `topic-setup` for Telegram forum topic creation and project wiring.

---
> Source: [pavel-molyanov/telegram-ai-agent](https://github.com/pavel-molyanov/telegram-ai-agent) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
