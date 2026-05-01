---
name: xmtp-cli
description: > Use when this capability is needed.
metadata:
  author: openclaw
---

# XMTP CLI

Use the `xmtp` command to test, debug, and interact with XMTP conversations, groups, and messages from the command line. This skill is the entry point; use the sub-skills below for specific CLI tasks.

## When to apply

- Testing or debugging XMTP from the command line
- Sending messages or creating and managing groups
- Listing or finding conversations, members, and messages
- Syncing conversations and messages
- Managing group permissions
- Demonstrating content types (text, markdown, attachment, transaction, deeplink, miniapp)

## Sub-skills

| Sub-skill | Use when |
|-----------|----------|
| **setup** | Initialize CLI and configure env (init, env variables) |
| **groups** | Create DM or group, update group metadata |
| **send** | Send messages to address or group |
| **list** | List conversations, members, messages; find by address or inbox |
| **debug** | Get info, resolve address, inspect inbox |
| **sync** | Sync conversations or sync all |
| **permissions** | List/info group permissions, update permissions |
| **content** | Demo text, markdown, attachment, transaction, deeplink, miniapp |
| **debugging** | Enable CLI debug logging (XMTP_FORCE_DEBUG env) |

## How to use

1. Pick the sub-skill that matches the task (e.g. send message → **send**).
2. Read that sub-skill’s `SKILL.md` and its `rules/` for step-by-step guidance.

## Install

```bash
npm install -g @xmtp/cli
# or
pnpm add -g @xmtp/cli
# or
yarn global add @xmtp/cli
```

## Run without install

```bash
npx @xmtp/cli <command> <arguments>
# or pnpx / yarn dlx
```

## Help

```bash
xmtp --help
xmtp <command> --help
```

Full documentation: [docs.xmtp.org](https://docs.xmtp.org)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
