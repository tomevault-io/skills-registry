---
name: bird
description: Post and interact with Bluesky social network via the AT Protocol API. Use when this capability is needed.
metadata:
  author: kody-w
---

# Bird (Bluesky)

Post to and interact with Bluesky via the AT Protocol.

## Authentication

Set environment variables:
- `BLUESKY_HANDLE` — your handle (e.g., user.bsky.social)
- `BLUESKY_APP_PASSWORD` — app password from Settings > App Passwords

## Create a Post

```bash
curl -s -X POST "https://bsky.social/xrpc/com.atproto.repo.createRecord" \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "repo": "'$BLUESKY_HANDLE'",
    "collection": "app.bsky.feed.post",
    "record": {
      "$type": "app.bsky.feed.post",
      "text": "Hello from openrappter!",
      "createdAt": "'$(date -u +%Y-%m-%dT%H:%M:%S.000Z)'"
    }
  }'
```

## Get Timeline

```bash
curl -s "https://bsky.social/xrpc/app.bsky.feed.getTimeline" \
  -H "Authorization: Bearer $ACCESS_TOKEN"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
