---
name: whatsapp
description: WhatsApp automation using Green API. Send messages, voice notes, images, and get group members. Use when this capability is needed.
metadata:
  author: aviz85
---

# WhatsApp Automation (Green API)

> **First time?** If `setup_complete: false` above, run `./SETUP.md` first, then set `setup_complete: true`.

Send messages and get group information via WhatsApp.

## Workflow

1. **Get contact** - Use `get-contact` skill or ask user for phone
2. **Send message** - Text, voice, image, or file
3. **Confirm delivery** - Check response for success

## Scripts

All scripts in `scripts/` folder:

| Script | Use |
|--------|-----|
| `send-message.ts` | Text messages |
| `send-voice.ts` | Voice notes (converts to OGG) |
| `send-image.ts` | Images with captions |
| `get-group-members.ts` | Extract group phone numbers |

## Quick Examples

```bash
cd scripts/

# Text message
npx ts-node send-message.ts --phone "972501234567" --message "Hello!"

# Voice note
npx ts-node send-voice.ts --phone "972501234567" --audio "/path/audio.mp3"

# Image with caption
npx ts-node send-image.ts --phone "972501234567" --image "/path/image.jpg" --caption "Check this!"

# Preview without sending
npx ts-node send-message.ts --phone "972501234567" --message "Test" --dry-run
```

## Phone Formats

| Input | Normalized |
|-------|------------|
| `0501234567` | `972501234567@c.us` |
| `+972501234567` | `972501234567@c.us` |
| `972501234567` | `972501234567@c.us` |

## Default Numbers

Configure your test number in skill for quick access:

| Alias | Number |
|-------|--------|
| **myself / me / test** | `YOUR_PHONE_NUMBER` |

## Notes

- Use `--dry-run` to preview before bulk operations
- Voice notes require `ffmpeg` installed
- Rate limits apply when sending many messages

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aviz85) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
