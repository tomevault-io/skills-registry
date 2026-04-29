---
name: bluesky
description: Read, post, and interact with Bluesky (AT Protocol) via CLI. Use when user asks to check Bluesky, post to Bluesky, view their Bluesky timeline, search Bluesky, or check Bluesky notifications. Supports timeline, posting, profile lookup, search, and notifications. Use when this capability is needed.
metadata:
  author: sundial-org
---

# Bluesky CLI

Interact with Bluesky/AT Protocol from the command line.

## Setup

First-time setup requires an app password from Bluesky:
1. Go to bsky.app → Settings → Privacy and Security → App Passwords
2. Create a new app password
3. Run: `bsky login --handle yourhandle.bsky.social --password xxxx-xxxx-xxxx-xxxx`

**Security:** Password is NOT stored. The CLI exports a session token on login, which auto-refreshes. Your app password only exists in memory during login.

## Commands

```bash
# Authentication
bsky login --handle user.bsky.social --password xxxx-xxxx-xxxx-xxxx
bsky whoami

# Timeline
bsky timeline              # Show home feed (10 posts)
bsky timeline -n 20        # Show 20 posts
bsky tl                    # Alias

# Posting
bsky post "Hello world!"   # Create a post
bsky p "Short post"        # Alias
bsky post "Test" --dry-run # Preview without posting

# Version
bsky --version             # Show version

# Delete
bsky delete <post_id>      # Delete a post by ID or URL
bsky rm <url>              # Alias

# Profiles
bsky profile               # Your profile
bsky profile @someone.bsky.social

# Search
bsky search "query"        # Search posts
bsky search "offsec" -n 20

# Notifications
bsky notifications         # Likes, reposts, follows, mentions
bsky notif -n 30           # Alias with count
```

## Output Format

Timeline and search results show:
```
@handle · Jan 25 14:30
  Post text (truncated to 200 chars)
  ❤️ likes  🔁 reposts  💬 replies
  🔗 https://bsky.app/profile/handle/post/id
```

## Installation

The skill uses a Python virtual environment. On first run:
```bash
cd {baseDir}/scripts
python3 -m venv venv
./venv/bin/pip install atproto
```

Then run commands via:
```bash
{baseDir}/scripts/venv/bin/python {baseDir}/scripts/bsky.py [command]
```

Or use the wrapper script:
```bash
{baseDir}/scripts/bsky [command]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sundial-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
