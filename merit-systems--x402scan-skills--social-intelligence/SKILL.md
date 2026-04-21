---
name: social-intelligence
description: | Use when this capability is needed.
metadata:
  author: merit-systems
---

# Social Intelligence with x402 APIs

> **STOP — Read before making any API call.** enrichx402.com endpoints are **not** the same as each provider's native API. All paths use the format `https://enrichx402.com/api/{provider}/{action}`. You MUST either:
> 1. Copy exact URLs from the Quick Reference table below, OR
> 2. Run `x402.discover_api_endpoints(url="https://enrichx402.com")` to get the correct paths
>
> **Guessing paths will fail** with 405 errors (wrong path) or 404 errors (missing `/api/` prefix).

Access X/Twitter (via Grok) and Reddit through x402-protected endpoints.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price | Description |
|------|----------|-------|-------------|
| Search X posts | `https://enrichx402.com/api/grok/x-search` | $0.02 | Search tweets by keywords |
| Find X users | `https://enrichx402.com/api/grok/user-search` | $0.02 | Search users by criteria |
| Get user posts | `https://enrichx402.com/api/grok/user-posts` | $0.02 | Recent posts from user |
| Search Reddit | `https://enrichx402.com/api/reddit/search` | $0.02 | Search Reddit posts |
| Get comments | `https://enrichx402.com/api/reddit/post-comments` | $0.02 | Comments on a post |

See [rules/rate-limits.md](rules/rate-limits.md) for usage guidance.

## X/Twitter via Grok

### Search Posts

Search for X posts by keywords:

```mcp
x402.fetch(
  url="https://enrichx402.com/api/grok/x-search",
  method="POST",
  body={
    "query": "AI agents"
  }
)
```

**Parameters:**
- `query` - Search keywords (required)

**Returns:**
- Post text and author info
- Engagement metrics (likes, retweets, replies)
- Timestamps and URLs
- Media attachments

### Search Users

Find X users matching criteria:

```mcp
x402.fetch(
  url="https://enrichx402.com/api/grok/user-search",
  method="POST",
  body={
    "query": "AI researcher San Francisco"
  }
)
```

**Returns:**
- Username and display name
- Bio/description
- Follower/following counts
- Verification status
- Profile links

### Get User's Posts

Fetch recent posts from a specific user:

```mcp
x402.fetch(
  url="https://enrichx402.com/api/grok/user-posts",
  method="POST",
  body={
    "username": "elonmusk"
  }
)
```

**Parameters:**
- `username` - X username without @ (required)

**Returns:** Recent posts with full engagement metrics.

## Reddit

### Search Posts

Search Reddit for posts:

```mcp
x402.fetch(
  url="https://enrichx402.com/api/reddit/search",
  method="POST",
  body={
    "query": "best programming languages 2024"
  }
)
```

**Parameters:**
- `query` - Search terms (required)
- `subreddit` - Limit to specific subreddit
- `sort` - relevance, hot, new, top
- `time` - hour, day, week, month, year, all

**Returns:**
- Post title and content
- Author and subreddit
- Upvotes and comment count
- Post URL

### Search in Subreddit

```mcp
x402.fetch(
  url="https://enrichx402.com/api/reddit/search",
  method="POST",
  body={
    "query": "typescript vs javascript",
    "subreddit": "programming",
    "sort": "top",
    "time": "year"
  }
)
```

### Get Post Comments

Get comments from a Reddit post:

```mcp
x402.fetch(
  url="https://enrichx402.com/api/reddit/post-comments",
  method="POST",
  body={
    "postUrl": "https://reddit.com/r/programming/comments/abc123/..."
  }
)
```

**Returns:**
- Comment text and author
- Upvotes/downvotes
- Reply threads
- Comment timestamps

## Workflows

### Standard

1. (Optional) Check balance: `x402.get_wallet_info`
2. **Discover endpoints (required before first fetch):** `x402.discover_api_endpoints(url="https://enrichx402.com")`
3. Check endpoint schema: `x402.check_endpoint_schema(url="...")` to verify parameters and pricing
4. Call endpoint with `x402.fetch` using exact URL from discovery or Quick Reference table above
5. Parse and present results

### Brand Monitoring

- [ ] (Optional) Check balance: `x402.get_wallet_info`
- [ ] Search X for brand mentions
- [ ] Search Reddit for discussions
- [ ] Summarize sentiment and key mentions

```mcp
x402.fetch(
  url="https://enrichx402.com/api/grok/x-search",
  method="POST",
  body={"query": "YourBrand OR @YourBrand"}
)
```

```mcp
x402.fetch(
  url="https://enrichx402.com/api/reddit/search",
  method="POST",
  body={"query": "YourBrand", "sort": "new"}
)
```

### Competitor Research

- [ ] Search Reddit for competitor reviews
- [ ] Search X for competitor mentions
- [ ] Analyze common complaints and praise

```mcp
x402.fetch(
  url="https://enrichx402.com/api/reddit/search",
  method="POST",
  body={"query": "competitor name review", "sort": "top", "time": "year"}
)
```

### Influencer Discovery

- [ ] Define criteria (topic, follower range)
- [ ] Search for matching users
- [ ] Get recent posts for top candidates

```mcp
x402.fetch(
  url="https://enrichx402.com/api/grok/user-search",
  method="POST",
  body={"query": "tech blogger 100k followers"}
)
```

### Community Sentiment

- [ ] Identify relevant subreddit
- [ ] Search for discussions on topic
- [ ] Get comments from top posts
- [ ] Synthesize overall sentiment

```mcp
x402.fetch(
  url="https://enrichx402.com/api/reddit/search",
  method="POST",
  body={"query": "new feature name", "subreddit": "relevant_community", "sort": "hot"}
)
```

```mcp
x402.fetch(
  url="https://enrichx402.com/api/reddit/post-comments",
  method="POST",
  body={"postUrl": "https://reddit.com/..."}
)
```

## Response Data

### X/Twitter Post Fields
- `text` - Post content
- `author` - Username, display name, verified status
- `metrics` - Likes, retweets, replies, quotes, views
- `createdAt` - Timestamp
- `url` - Link to post
- `media` - Attached images/videos

### X/Twitter User Fields
- `username` - Handle without @
- `displayName` - Full name
- `description` - Bio
- `followers` / `following` - Counts
- `verified` - Verification status
- `profileImageUrl` - Avatar

### Reddit Post Fields
- `title` - Post title
- `selftext` - Post body (for text posts)
- `author` - Username
- `subreddit` - Subreddit name
- `score` - Upvotes minus downvotes
- `numComments` - Comment count
- `url` - Link to post
- `createdUtc` - Timestamp

### Reddit Comment Fields
- `body` - Comment text
- `author` - Username
- `score` - Net upvotes
- `replies` - Nested replies
- `createdUtc` - Timestamp

## Cost Estimation

| Task | Calls | Cost |
|------|-------|------|
| Quick X search | 1 | $0.02 |
| User profile + posts | 2 | $0.04 |
| Reddit thread + comments | 2 | $0.04 |
| Full monitoring scan | 4-6 | $0.08-0.12 |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/merit-systems) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
