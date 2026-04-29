---
name: facebook
description: Query Facebook — pages, posts, insights, and comments via the Graph API. Use when this capability is needed.
metadata:
  author: thinkfleetai
---

# Facebook

Query pages, posts, and insights via the Facebook Graph API.

## Environment Variables

- `FACEBOOK_ACCESS_TOKEN` - Page or user access token

## Get page info

```bash
curl -s "https://graph.facebook.com/v19.0/PAGE_ID?fields=name,fan_count,followers_count&access_token=$FACEBOOK_ACCESS_TOKEN" | jq '{name, fan_count, followers_count}'
```

## List page posts

```bash
curl -s "https://graph.facebook.com/v19.0/PAGE_ID/posts?fields=message,created_time,likes.summary(true),comments.summary(true)&limit=10&access_token=$FACEBOOK_ACCESS_TOKEN" | jq '.data[] | {id, message, created_time, likes: .likes.summary.total_count, comments: .comments.summary.total_count}'
```

## Get page insights

```bash
curl -s "https://graph.facebook.com/v19.0/PAGE_ID/insights?metric=page_impressions,page_engaged_users&period=day&access_token=$FACEBOOK_ACCESS_TOKEN" | jq '.data[] | {name, values: .values[-1]}'
```

## Notes

- Page access token required for page management.
- Always confirm before posting content.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkfleetai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
