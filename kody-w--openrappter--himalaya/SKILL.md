---
name: himalaya
description: Read, send, and manage email via the Himalaya CLI email client. Use when this capability is needed.
metadata:
  author: kody-w
---

# Himalaya

CLI email client for reading and sending email.

## List Messages

```bash
himalaya list --folder INBOX --page-size 10
```

## Read a Message

```bash
himalaya read --folder INBOX 123
```

## Send Email

```bash
himalaya send --from "me@example.com" --to "you@example.com" --subject "Hello" --body "Message content"
```

## Search

```bash
himalaya search --folder INBOX "keyword"
```

## Manage Folders

```bash
himalaya folder list
himalaya folder create "New Folder"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
