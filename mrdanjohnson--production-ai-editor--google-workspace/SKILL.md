---
name: google-workspace
description: Access Google Calendar, Gmail, and other Google Workspace APIs. Use when the user needs to read or manage Google Calendar events, read/send/search Gmail messages, or access other Google Workspace data. Requires OAuth2 setup via Google Cloud Console. Use when this capability is needed.
metadata:
  author: mrdanjohnson
---

# Google Workspace Skill

Access Google Calendar, Gmail, and other Google Workspace services via official APIs.

## Setup Required (One-Time)

### 1. Google Cloud Console Setup

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project (or select existing)
3. Enable APIs:
   - **Google Calendar API**
   - **Gmail API**
4. Create OAuth2 credentials:
   - APIs & Services → Credentials → Create Credentials → OAuth client ID
   - Application type: **Desktop app**
   - Download the `client_secret.json` file

### 2. Store Credentials

Save the downloaded file to:
```
~/.config/openclaw/google-workspace/client_secret.json
```

### 3. Authenticate

Run the auth script to generate a token:
```bash
python3 ~/.openclaw/workspace/skills/google-workspace/scripts/google_auth.py
```

This will open a browser for OAuth consent. The token will be saved to:
```
~/.config/openclaw/google-workspace/token.json
```

## Usage

### Calendar

**List upcoming events:**
```bash
python3 scripts/calendar_ops.py list --max-results 10
```

**Get events for a specific date:**
```bash
python3 scripts/calendar_ops.py list --date 2026-02-15
```

**Create an event:**
```bash
python3 scripts/calendar_ops.py create \
  --summary "Team Meeting" \
  --start "2026-02-05T14:00:00" \
  --end "2026-02-05T15:00:00" \
  --description "Weekly sync" \
  --attendees "colleague@example.com"
```

**Check availability:**
```bash
python3 scripts/calendar_ops.py freebusy \
  --start "2026-02-05T09:00:00" \
  --end "2026-02-05T17:00:00"
```

### Gmail

**List recent emails:**
```bash
python3 scripts/gmail_ops.py list --max-results 10
```

**Search emails:**
```bash
python3 scripts/gmail_ops.py search "from:boss@company.com subject:urgent"
```

**Read an email:**
```bash
python3 scripts/gmail_ops.py read <message-id>
```

**Send an email:**
```bash
python3 scripts/gmail_ops.py send \
  --to "recipient@example.com" \
  --subject "Hello" \
  --body "Message content here"
```

**Get unread count:**
```bash
python3 scripts/gmail_ops.py unread-count
```

## Authentication

The scripts automatically handle token refresh. If authentication fails:

1. Delete `~/.config/openclaw/google-workspace/token.json`
2. Re-run the auth script

## Troubleshooting

- **"Token expired"**: Delete token.json and re-authenticate
- **"API not enabled"**: Check that Calendar/Gmail APIs are enabled in Google Cloud Console
- **"Insufficient permissions"**: Ensure you selected the required scopes during OAuth

## Security Notes

- Never commit `client_secret.json` or `token.json` to git
- These files contain sensitive credentials
- The default token storage is `~/.config/openclaw/google-workspace/`

## Reference

See [references/api-guide.md](references/api-guide.md) for detailed API documentation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mrdanjohnson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
