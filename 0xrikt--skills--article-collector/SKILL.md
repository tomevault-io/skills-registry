---
name: article-collector
description: Collect articles from URLs, extract key insights using AI, and store them in Notion database. Use when user sends article links (WeChat, Twitter, web pages) to automatically save and summarize content. Use when this capability is needed.
metadata:
  author: 0xrikt
---

# Article Collector

Automatically collect, summarize, and store articles from URLs in a Notion database.

## ⚠️ Setup Required

Before using this skill, users must complete the setup in `README.md`:
1. Create a Notion Integration and save API key to `~/.config/notion/api_key`
2. Create a Notion database with required fields
3. Configure the database ID in their config

## Workflow

When a URL is detected in the message:

1. **Extract URL** - Identify article links (http:// or https://)
2. **Fetch Content** - Use HTTP requests or MCP browser tools to get article content
3. **Summarize** - Use AI to extract key insights (summary + 3-5 key points)
4. **Store** - Save to Notion database with structured fields
5. **Confirm** - Reply with brief confirmation

## Source Detection

- **WeChat**: URL contains `mp.weixin.qq.com` → source: "微信公众号"
- **Twitter/X**: URL contains `twitter.com` or `x.com` → source: "Twitter"
- **Other**: Default to "网页"

## Notion Storage

### Database Configuration

Database ID should be configured by the user. Check these locations (in order):

1. `skills.entries.article-collector.databaseId` in `~/.clawdbot/moltbot.json`
2. User's custom configuration file

To get the database ID from config:
```bash
cat ~/.clawdbot/moltbot.json | jq -r '.skills.entries["article-collector"].databaseId'
```

**If database ID is not found, prompt the user to configure it first.**

### Field Mapping

Required fields in the user's Notion database:

- **标题** (Title) - Article title
- **链接** (URL) - Original article URL
- **来源** (Select) - "微信公众号" / "Twitter" / "网页"
- **摘要** (Text) - AI-generated summary (200-300 words)
- **重点** (Text) - Key points (3-5 items, 1-2 sentences each)
- **收藏日期** (Date) - Current date (ISO format: YYYY-MM-DD)
- **推送状态** (Checkbox) - false (optional)

### API Call Example

```bash
# Get credentials from user's config
NOTION_KEY=$(cat ~/.config/notion/api_key)
DB_ID=$(cat ~/.clawdbot/moltbot.json | jq -r '.skills.entries["article-collector"].databaseId')

# Format database ID (add dashes if needed)
# 32-char ID → 8-4-4-4-12 format
DB_ID_FORMATTED="${DB_ID:0:8}-${DB_ID:8:4}-${DB_ID:12:4}-${DB_ID:16:4}-${DB_ID:20:12}"

curl -X POST "https://api.notion.com/v1/pages" \
  -H "Authorization: Bearer $NOTION_KEY" \
  -H "Notion-Version: 2022-06-28" \
  -H "Content-Type: application/json" \
  -d '{
    "parent": {"database_id": "'"$DB_ID_FORMATTED"'"},
    "properties": {
      "标题": {"title": [{"text": {"content": "Article Title"}}]},
      "链接": {"url": "https://example.com/article"},
      "来源": {"select": {"name": "网页"}},
      "摘要": {"rich_text": [{"text": {"content": "Summary text..."}}]},
      "重点": {"rich_text": [{"text": {"content": "1. Point one\n2. Point two"}}]},
      "收藏日期": {"date": {"start": "2026-01-27"}}
    }
  }'
```

## Content Extraction

### Fetching

- Use HTTP requests for simple pages
- Use MCP browser tools for JavaScript-rendered content
- Extract main content, remove ads/navigation

### Summarization

Use AI with this prompt:

```
请提炼以下文章的核心内容：
1. 生成 200-300 字的摘要
2. 提取 3-5 条核心观点（每条 1-2 句话）
3. 如有重要数据或事实，单独列出

文章内容：[article content]
```

## Error Handling

- **Missing config**: Prompt user to complete setup (see README.md)
- **Fetch failure**: Ask user if they want to input manually
- **Storage failure**: Check API key and database ID, retry, inform user of specific error

## Response

Keep it concise:
- "已保存到收藏库 ✓"
- Optionally show title and 1-2 key points

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/0xrikt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
