---
name: mcp-server-weibo
description: Use when working with the `weibo-cli` is a command-line interface tool for interacting with Weibo data without using the MCP protocol. It provides direct access to all Weibo functionality through simple terminal commands.
metadata:
  author: qinyuanpei
---
# Weibo CLI Skills

## Overview

The `weibo-cli` is a command-line interface tool for interacting with Weibo data without using the MCP protocol. It provides direct access to all Weibo functionality through simple terminal commands.

## Installation

```bash
# Run with uvx (recommended)
uvx --from mcp-server-weibo weibo-cli --help

# Or run with uv tool
uv tool install mcp-server-weibo
weibo-cli --help
```

## Workflows

### User-Related Operations

For operations involving users (profile, feeds, followers, fans), you need to first search for the user to get their UID:

```
1. Search users → Get UID
       ↓
2. Use UID for subsequent operations
```

**Example: Get a specific user's feeds**
```bash
# Step 1: Search for user to get UID
weibo-cli users "雷军" -n 5

# Step 2: Use the UID (1749127163) to get feeds
weibo-cli feeds 1749127163 -n 10
```

**Example: Get someone's followers**
```bash
# Step 1: Search users
weibo-cli users "科技博主" -n 5

# Step 2: Use UID to get followers
weibo-cli followers <UID> -n 20
```

### Content Search

For searching posts/content by keyword, use the `search` command directly:

```bash
# Search posts containing keywords
weibo-cli search "人工智能" -n 20
```

### Topic Search

For searching hashtags/topics:

```bash
# Search topics
weibo-cli topics "演唱会" -n 10
```

### Trending Hot Searches

Get current trending topics directly:

```bash
# Get trending hot searches
weibo-cli trending -n 15
```

### Comments

Getting comments requires a `feed_id`, which can be obtained from feed results:

```
1. Search users → Get UID
       ↓
2. Get feeds → Get feed IDs
       ↓
3. Use feed_id to get comments
```

**Example: Get comments on a specific post**
```bash
# Step 1: Get the user's feeds
weibo-cli feeds 1749127163 -n 10

# Step 2: Use the feed id from results to get comments
weibo-cli comments <FEED_ID> -p 1
```

## CLI Commands

### profile

Get user profile information by UID.

```bash
weibo-cli profile <UID>
```

**Example:**
```bash
weibo-cli profile 1749127163
```

**Output:** JSON object with user details (id, screen_name, description, followers_count, etc.)

### feeds

Get user posts (weibo feeds).

```bash
weibo-cli feeds <UID> [-n LIMIT]
```

**Example:**
```bash
weibo-cli feeds 1749127163 -n 10
```

**Output:** JSON array of feed items with text, source, created_at, comments_count, attitudes_count, reposts_count, etc.

### search

Search for posts containing specific keywords.

```bash
weibo-cli search <KEYWORD> [-n LIMIT] [-p PAGE]
```

**Example:**
```bash
weibo-cli search "人工智能" -n 20 -p 1
```

**Output:** JSON array of matching posts with user info, timestamps, and engagement metrics.

### users

Search for Weibo users by keyword.

```bash
weibo-cli users <KEYWORD> [-n LIMIT] [-p PAGE]
```

**Example:**
```bash
weibo-cli users "科技" -n 10
```

**Output:** JSON array of user profiles matching the search term.

### topics

Search for Weibo topics (hashtags).

```bash
weibo-cli topics <KEYWORD> [-n LIMIT] [-p PAGE]
```

**Example:**
```bash
weibo-cli topics "演唱会" -n 10
```

**Output:** JSON array of topic items with title, description, and URL.

### trending

Get current trending/hot search items.

```bash
weibo-cli trending [-n LIMIT]
```

**Example:**
```bash
weibo-cli trending -n 15
```

**Output:** JSON array of trending items with rank, description, and热度值.

### comments

Get comments for a specific post.

```bash
weibo-cli comments <FEED_ID> [-p PAGE]
```

**Example:**
```bash
weibo-cli comments 5173507416919189 -p 1
```

**Output:** JSON array of comments with user info, text, and timestamps.

### followers

Get a user's followers.

```bash
weibo-cli followers <UID> [-n LIMIT] [-p PAGE]
```

**Example:**
```bash
weibo-cli followers 1749127163 -n 20
```

**Output:** JSON array of user profiles who follow this user.

### fans

Get a user's fans (people who follow this user).

```bash
weibo-cli fans <UID> [-n LIMIT] [-p PAGE]
```

**Example:**
```bash
weibo-cli fans 1749127163 -n 20
```

**Output:** JSON array of user profiles who are fans of this user.

## Common Options

| Option | Description | Default |
|--------|-------------|---------|
| `-n, --limit` | Maximum number of results | varies by command |
| `-p, --page` | Page number for pagination | 1 |

## Exit Codes

- `0`: Success
- `1`: Error (network failure, invalid arguments, etc.)

## Error Handling

All errors are logged to stderr and displayed as error messages. The CLI will exit with code 1 if any operation fails.

## Tips

1. **User Operations**: Always start with `users` to get the UID before using `profile`, `feeds`, `followers`, or `fans`
2. **Comments**: Get `feed_id` from `feeds` results before using `comments`
3. **Pagination**: Use `-p` option to navigate through multiple pages of results
4. **Result Limits**: Use `-n` option to control how many results to fetch
5. **JSON Output**: All results are output as formatted JSON for easy parsing

## See Also

- [MCP Server README](../src/mcp_server_weibo/README.md) - MCP protocol usage
- [Weibo API Documentation](https://m.weibo.cn/)

---
> Source: [qinyuanpei/mcp-server-weibo](https://github.com/qinyuanpei/mcp-server-weibo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-29 -->
