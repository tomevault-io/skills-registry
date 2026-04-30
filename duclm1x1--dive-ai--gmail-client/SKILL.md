---
name: gmail-client-pm
description: Read and send emails via Gmail. Use to list unread messages, read specific emails by ID, or send new emails. Use when this capability is needed.
metadata:
  author: duclm1x1
---

# Gmail Client

A simple Python-based tool to interact with Gmail using IMAP/SMTP.

## Configuration

Requires `GMAIL_USER` and `GMAIL_PASS` (App Password) environment variables.

## Usage

### List Unread Emails

```bash
python3 skills/gmail-client/scripts/gmail_tool.py list
```

### Read an Email

```bash
python3 skills/gmail-client/scripts/gmail_tool.py read <EMAIL_ID>
```

### Send an Email

```bash
python3 skills/gmail-client/scripts/gmail_tool.py send <TO> <SUBJECT> <BODY>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
