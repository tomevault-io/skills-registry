---
name: codexbar
description: | Use when this capability is needed.
metadata:
  author: lizhelang
---

# Codexbar CLI Skill

Use this skill when the task is about operating Codexbar accounts or OAuth flows. Prefer the CLI over the GUI.

## Resolve the binary

Use the first working path:

1. `codexbarctl`
2. `/Applications/codexbar.app/Contents/MacOS/codexbarctl`
3. Build from the repo:

```bash
xcodebuild -scheme codexbarctl -destination 'platform=macOS' build
```

Then invoke the built binary from Xcode's product directory if needed.

## Standard commands

Interactive human flow:

```bash
codexbarctl openai login
```

Automation / AI flow:

```bash
codexbarctl openai login start --json
codexbarctl openai login complete --flow-id <id> --callback-url <url> --json
```

If only the `code` parameter is available:

```bash
codexbarctl openai login complete --flow-id <id> --code <code> --json
```

List accounts:

```bash
codexbarctl accounts list --json
```

Activate an account:

```bash
codexbarctl accounts activate --account-id <id> --json
```

## Rules

- Prefer `openai login` for a person in a terminal.
- Prefer `login start` + `login complete` for AI or scriptable flows.
- Do not hand-edit `~/.codex/auth.json` or `~/.codex/config.toml` if the CLI is available.
- Never echo or summarize `access_token`, `refresh_token`, or `id_token`.

---
> Source: [lizhelang/codexbar](https://github.com/lizhelang/codexbar) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-03 -->
