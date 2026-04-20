---
name: sendmail
description: Send email notification when task is complete. This skill should be used when the user wants to be notified via email after finishing a task, or when explicitly invoking /sendmail. Use when this capability is needed.
metadata:
  author: xujialiu
---

# Send Email Notification

This skill sends an email notification to inform the user that a task has been completed.

## When to Use

- When the user explicitly requests email notification after task completion
- When invoking `/sendmail` command
- After completing a long-running task where the user requested to be notified

## Usage

To send a notification email after completing a task:

```bash
python3 ~/.claude/skills/sendmail/scripts/send_email.py \
  --subject "Task Complete" \
  --body "Your task has been completed successfully."
```

## Parameters

- `--to`: Recipient email address (optional if configured in .credentials.json)
- `--subject`: Email subject line
- `--body`: Email body text
- `--body-file`: Alternative to --body, read body from a file
- `--html`: Send as HTML email (optional)

## Example Notification

When a task completes, send a summary:

```bash
python3 ~/.claude/skills/sendmail/scripts/send_email.py \
  --subject "Claude Code: Task Completed" \
  --body "Task: <describe what was done>

Status: Completed successfully

Summary:
- <key point 1>
- <key point 2>
- <any relevant details>"
```

## Configuration

SMTP credentials and default recipient are stored in `~/.claude/.credentials.json` under the `smtp` key:

```json
{
  "smtp": {
    "host": "smtp.example.com",
    "port": 587,
    "user": "your-email@example.com",
    "password": "your-app-password",
    "to": "recipient@example.com"
  }
}
```

The `to` field sets the default recipient. Command-line `--to` overrides this value.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xujialiu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
