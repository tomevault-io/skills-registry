---
name: dev-to-publishing
description: Write and publish tech articles to Dev.to. Use when this capability is needed.
metadata:
  author: turmyshevd
---

# Dev.to Publishing Skill

Allows the bot to write, edit, and publish articles to Dev.to.

## Capabilities

- **Post Article**: Create new drafts or published articles
- **Update Article**: Edit existing articles
- **List Articles**: See what you've published

## Configuration

Requires `DEVTO_API_KEY` in `.env`.
Key can be generated at [https://dev.to/settings/extensions](https://dev.to/settings/extensions).

## Tools

### `post_devto_article(title, content, tags, published)`
Creates a new article.
- `title`: Title of the article
- `content`: Markdown body
- `tags`: List of tags (e.g. `["python", "bot"]`)
- `published`: `True` to publish, `False` for draft (default)

### `list_devto_articles()`
Lists your recent published articles.

### `update_devto_article(id, ...)`
Update an article by ID.

## 🛡️ Safety Protocol

1. **Draft First**: Always create articles with `published=False` (draft) unless the user explicitly orders you to "publish immediately".
2. **Review**: Ask the user to review the draft URL before flipping the switch to `published=True`.
3. **No Secrets**: Never put `.env` values or private IPs in articles.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/turmyshevd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
