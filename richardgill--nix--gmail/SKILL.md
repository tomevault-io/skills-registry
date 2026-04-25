---
name: gmail
description: Gmail CLI for searching emails, reading threads, sending messages, managing drafts, and handling labels/attachments. Use when this capability is needed.
metadata:
  author: richardgill
---

## Usage

Email style preference: start with "Hi" and end with "Thanks,\nRichard".

Run `gmcli --help` for full command reference.

First: run `gmcli accounts list` to find which emails exist. If there's only one, use that.

Common operations:
- `gmcli <email> search "<query>"` - Search emails using Gmail query syntax
- `gmcli <email> thread <threadId>` - Read a thread with all messages
- `gmcli <email> url <threadIds...>` - Generate canonical Gmail thread URL(s); prefer this over manually constructing links
- URL format returned: `https://mail.google.com/mail/?authuser=<email>#all/<threadId>`
- `gmcli <email> send --to <emails> --subject <s> --body <b>` - Send email
- For newlines in `--body`, use Bash ANSI-C quoting like `--body $'Line 1\n\nLine 2'` or paste literal newlines
- `gmcli <email> labels list` - List all labels
- `gmcli <email> drafts list` - List drafts

## Data Storage

- `~/.gmcli/credentials.json` - OAuth client credentials
- `~/.gmcli/accounts.json` - Account tokens
- `~/.gmcli/attachments/` - Downloaded attachments

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/richardgill) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
