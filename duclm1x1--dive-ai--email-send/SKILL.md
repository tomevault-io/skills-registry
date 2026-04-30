---
name: email-send
description: Send a quick email via SMTP using `msmtp` without opening a full mail client. Use when this capability is needed.
metadata:
  author: duclm1x1
---

# Email Send Skill

Send a quick email via SMTP without opening the full himalaya client. Requires `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS` env vars.

## Sending Email

Send a basic email:

```bash
echo "Meeting at 3pm tomorrow." | msmtp recipient@example.com
```

Send with subject and headers:

```bash
printf "To: recipient@example.com\nSubject: Quick update\n\nHey, the deploy is done." | msmtp recipient@example.com
```

## Options

- `--cc` -- carbon copy recipients
- `--bcc` -- blind carbon copy recipients
- `--attach <file>` -- attach a file

## Install

```bash
sudo dnf install msmtp
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
