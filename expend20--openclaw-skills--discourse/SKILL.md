---
name: discourse
description: Interact with Discourse forum REST API. Use for reading/creating/editing topics, posts, categories, users, groups, tags, search, notifications, badges, invites, uploads, backups, private messages, and calendar events on any Discourse instance. Triggers on any mention of Discourse, forum management, community posts, or forum API calls. Use when this capability is needed.
metadata:
  author: expend20
---

# Discourse REST API

Call any Discourse REST API endpoint via the bundled `scripts/discourse-api.sh` wrapper.

## Setup

Set environment variables before use:

```bash
export DISCOURSE_URL=https://your-forum.example.com
export DISCOURSE_API_KEY=your-api-key
export DISCOURSE_API_USERNAME=system  # optional, defaults to "system"
```

Generate an API key from your Discourse admin panel: `/admin/api/keys`.

## Quick Reference

```bash
# Common reads
discourse-api.sh GET /categories.json
discourse-api.sh GET /latest.json
discourse-api.sh GET /t/{topic_id}.json
discourse-api.sh GET /posts/{id}.json
discourse-api.sh GET /search.json?q=search+term
discourse-api.sh GET /u/{username}.json

# Create topic (always include "goodie" tag)
discourse-api.sh POST /posts.json -d '{"title":"My Topic","raw":"Body text","category":1,"tags":["goodie"]}'

# Create reply
discourse-api.sh POST /posts.json -d '{"topic_id":123,"raw":"Reply text"}'

# Edit post
discourse-api.sh PUT /posts/{id}.json -d '{"post":{"raw":"Updated text"}}'

# Delete
discourse-api.sh DELETE /posts/{id}.json
discourse-api.sh DELETE /t/{id}.json
```

## Authentication

All mutating endpoints and most reads require:
- `Api-Key` header (from admin panel)
- `Api-Username` header (user to act as)

The script handles both automatically from env vars.

## Content-Type

POST/PUT accept `application/json`, `multipart/form-data`, or `application/x-www-form-urlencoded`. The script defaults to JSON.

## API Categories (93 endpoints)

Load the relevant reference file for detailed endpoint info:

| Category | File | Endpoints |
|----------|------|-----------|
| Backups | [references/backups.md](references/backups.md) | 4 |
| Badges | [references/badges.md](references/badges.md) | 5 |
| Categories | [references/categories.md](references/categories.md) | 5 |
| Calendar Events | [references/discourse-calendar-events.md](references/discourse-calendar-events.md) | 2 |
| Groups | [references/groups.md](references/groups.md) | 9 |
| Invites | [references/invites.md](references/invites.md) | 2 |
| Notifications | [references/notifications.md](references/notifications.md) | 2 |
| Posts | [references/posts.md](references/posts.md) | 8 |
| Private Messages | [references/private-messages.md](references/private-messages.md) | 2 |
| Search | [references/search.md](references/search.md) | 1 |
| Site | [references/site.md](references/site.md) | 2 |
| Tags | [references/tags.md](references/tags.md) | 6 |
| Topics | [references/topics.md](references/topics.md) | 14 |
| Uploads | [references/uploads.md](references/uploads.md) | 7 |
| Users | [references/users.md](references/users.md) | 24 |

## Rules

- **Always include the `"goodie"` tag** when creating topics. Add it to the `tags` array alongside any other tags: `"tags":["goodie", ...]`

## Boolean Values

Always use lowercase `true` or `false` for boolean params.

## Pagination

Use `Accept: application/json` header (already set by script) when following pagination URLs returned by the API, as they lack the `.json` suffix.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/expend20) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
