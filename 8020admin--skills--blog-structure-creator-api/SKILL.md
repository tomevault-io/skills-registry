---
name: blog-structure-creator-api
description: Create complete blog CMS structure on a Webflow project using the Webflow Data API v2 (REST). Same outcome as blog-structure-creator but via API instead of MCP. Use when the user wants to create blog CMS structure in Webflow with the API, or when MCP is unavailable. Use when this capability is needed.
metadata:
  author: 8020admin
---

# Blog Structure CMS Setup (Webflow API)

Create the same blog CMS infrastructure as the **blog-structure-creator** skill (MCP-based) using the **Webflow Data API v2** only. No MCP—all operations are HTTP requests to `https://api.webflow.com/v2`.

Reference: [Webflow CMS API](https://developers.webflow.com/data/v2.0.0/reference/cms).

## Important Note

**CRITICAL: This skill creates collections WITH SEO fields and help text on ALL collections. Do NOT skip Meta Title, Meta Description, and OG Image fields. Do NOT skip helpText on any field.**

**Use only the Webflow Data API v2** for all operations. Implement with HTTP (e.g. `curl`, `fetch`, or `axios`) or the official Webflow SDK. Do not use Webflow MCP tools.

**Authentication:** Every request must include header `Authorization: Bearer <token>`. The user must provide a Webflow API token with `cms:read` and `cms:write` (e.g. via `WEBFLOW_ACCESS_TOKEN`). Never hardcode tokens. For listing sites, an account-level token is required; for site-specific calls, use the site ID in the path.

## What This Creates

Same four collections as blog-structure-creator:

1. **Categories** – taxonomy with SEO fields  
2. **Tags** – taxonomy with SEO fields  
3. **Authors** – author profiles with SEO fields  
4. **Blog Posts** – main content with references to Authors, Categories, Tags  

All fields include the exact `helpText` specified below. Categories, Tags, and Authors each have Meta Title, Meta Description, and OG Image.

## API Endpoints Used

| Action | Method | Path |
|--------|--------|------|
| List sites | GET | `/sites` |
| Get site | GET | `/sites/:site_id` |
| List collections | GET | `/sites/:site_id/collections` |
| Create collection | POST | `/sites/:site_id/collections` |
| Get collection | GET | `/collections/:collection_id` |
| Create field | POST | `/collections/:collection_id/fields` |

Base URL: `https://api.webflow.com/v2`

## Process

### Phase 1: Site setup

1. **List sites**: `GET /sites` to get available sites. If user did not provide a site ID, ask them to choose one.
2. **Get site**: `GET /sites/:site_id` to confirm access and check plan/limits.
3. **List collections**: `GET /sites/:site_id/collections` to check for existing names (e.g. "Categories", "Tags", "Authors", "Blog Posts"). If any conflict, stop and report.

### Phase 2: Preview and confirmation

4. **Show structure**: Display the four collections and their fields (same as blog-structure-creator: Categories 6 fields, Tags 5, Authors 8, Blog Posts 12 static + 3 reference).
5. **Confirm plan**: Ensure the site plan allows at least 4 new collections.
6. **Get approval**: Wait for explicit user confirmation before creating anything.

### Phase 3: Collection creation via API

Create in this order so reference targets exist before Blog Posts.

**7. Create Categories**

`POST /sites/:site_id/collections` with body:

```json
{
  "displayName": "Categories",
  "singularName": "Category",
  "slug": "categories",
  "fields": [
    { "type": "PlainText", "displayName": "Name", "helpText": "The display name of the category.", "isRequired": true },
    { "type": "PlainText", "displayName": "Slug", "helpText": "URL-friendly version of the name (e.g., 'web-development'). Must be unique.", "isRequired": true },
    { "type": "RichText", "displayName": "Description", "helpText": "Optional description of the category." },
    { "type": "PlainText", "displayName": "Meta Title", "helpText": "SEO title for search engines (50-60 characters recommended)." },
    { "type": "PlainText", "displayName": "Meta Description", "helpText": "SEO description for search engines (150-160 characters recommended)." },
    { "type": "Image", "displayName": "OG Image", "helpText": "Open Graph image for social sharing (1200x630px recommended)." }
  ]
}
```

Store the returned collection `id` as `categoriesCollectionId`.

**8. Create Tags**

`POST /sites/:site_id/collections`:

```json
{
  "displayName": "Tags",
  "singularName": "Tag",
  "slug": "tags",
  "fields": [
    { "type": "PlainText", "displayName": "Name", "helpText": "The display name of the tag.", "isRequired": true },
    { "type": "PlainText", "displayName": "Slug", "helpText": "URL-friendly version of the name (e.g., 'javascript'). Must be unique.", "isRequired": true },
    { "type": "PlainText", "displayName": "Meta Title", "helpText": "SEO title for search engines (50-60 characters recommended)." },
    { "type": "PlainText", "displayName": "Meta Description", "helpText": "SEO description for search engines (150-160 characters recommended)." },
    { "type": "Image", "displayName": "OG Image", "helpText": "Open Graph image for social sharing (1200x630px recommended)." }
  ]
}
```

Store the returned `id` as `tagsCollectionId`.

**9. Create Authors**

`POST /sites/:site_id/collections`:

```json
{
  "displayName": "Authors",
  "singularName": "Author",
  "slug": "authors",
  "fields": [
    { "type": "PlainText", "displayName": "Name", "helpText": "The author's full name.", "isRequired": true },
    { "type": "PlainText", "displayName": "Slug", "helpText": "URL-friendly version of the name (e.g., 'john-smith'). Must be unique.", "isRequired": true },
    { "type": "RichText", "displayName": "Bio", "helpText": "Author biography." },
    { "type": "Image", "displayName": "Avatar", "helpText": "Author profile photo." },
    { "type": "Email", "displayName": "Email", "helpText": "Author's email address (optional)." },
    { "type": "PlainText", "displayName": "Meta Title", "helpText": "SEO title for search engines (50-60 characters recommended)." },
    { "type": "PlainText", "displayName": "Meta Description", "helpText": "SEO description for search engines (150-160 characters recommended)." },
    { "type": "Image", "displayName": "OG Image", "helpText": "Open Graph image for social sharing (1200x630px recommended)." }
  ]
}
```

Store the returned `id` as `authorsCollectionId`.

**10. Create Blog Posts (static fields only)**

`POST /sites/:site_id/collections`:

```json
{
  "displayName": "Blog Posts",
  "singularName": "Blog Post",
  "slug": "blog-posts",
  "fields": [
    { "type": "PlainText", "displayName": "Title", "helpText": "The main title of the blog post.", "isRequired": true },
    { "type": "PlainText", "displayName": "Slug", "helpText": "URL-friendly version of the title (e.g., 'getting-started-with-webflow'). Must be unique.", "isRequired": true },
    { "type": "RichText", "displayName": "Content", "helpText": "The main body content of the blog post.", "isRequired": true },
    { "type": "PlainText", "displayName": "Excerpt", "helpText": "Short summary of the post (150-200 characters recommended). Used in post listings and previews." },
    { "type": "Image", "displayName": "Featured Image", "helpText": "Main hero image for the post." },
    { "type": "PlainText", "displayName": "Featured Image Alt Text", "helpText": "Descriptive alt text for the featured image for accessibility and SEO." },
    { "type": "Image", "displayName": "Thumbnail Image", "helpText": "Smaller image for use in cards and listings." },
    { "type": "DateTime", "displayName": "Published Date", "helpText": "Publication date of the post. Important for SEO and content chronology.", "isRequired": true },
    { "type": "PlainText", "displayName": "Meta Title", "helpText": "SEO title for search engines (50-60 characters recommended). Include target keywords." },
    { "type": "PlainText", "displayName": "Meta Description", "helpText": "SEO description for search engines (150-160 characters recommended). Should encourage clicks and include target keywords naturally." },
    { "type": "Image", "displayName": "OG Image", "helpText": "Open Graph image for social sharing (1200x630px recommended)." },
    { "type": "PlainText", "displayName": "Robots", "helpText": "Search engine indexing directive (e.g., 'index, follow', 'noindex, nofollow'). Leave empty for default behavior (index, follow). Use 'noindex' for thin or duplicate content." }
  ]
}
```

Store the returned `id` as `blogPostsCollectionId`.

**11. Add reference fields to Blog Posts**

For each of the three fields below, call `POST /collections/:blogPostsCollectionId/fields` with the body shown.

Author (single reference):

```json
{
  "type": "Reference",
  "displayName": "Author",
  "helpText": "The author of this post. Each post must have one author.",
  "isRequired": true,
  "metadata": { "collectionId": "<authorsCollectionId>" }
}
```

Categories (multi-reference):

```json
{
  "type": "MultiReference",
  "displayName": "Categories",
  "helpText": "Primary categories for this post. Used for main navigation and filtering.",
  "metadata": { "collectionId": "<categoriesCollectionId>" }
}
```

Tags (multi-reference):

```json
{
  "type": "MultiReference",
  "displayName": "Tags",
  "helpText": "Tags for this post. Used for secondary categorization and related post suggestions.",
  "metadata": { "collectionId": "<tagsCollectionId>" }
}
```

Replace `<authorsCollectionId>`, `<categoriesCollectionId>`, and `<tagsCollectionId>` with the IDs returned in steps 7–9.

### Phase 4: Verification

12. **Verify**: Call `GET /collections/:id` for each of the four collection IDs and confirm field counts and types.
13. **Report**: Return all four collection IDs to the user.
14. **Confirm**: State that the blog structure is ready and suggest next steps (e.g. import content, design templates).

## Implementation options

- **curl**: Run one `curl` per request with `-H "Authorization: Bearer $WEBFLOW_ACCESS_TOKEN"` and `-H "Content-Type: application/json"`. Use `-d @payload.json` for POST bodies.
- **Script**: Generate a short Node (fetch/axios) or Python script that the user runs with `WEBFLOW_ACCESS_TOKEN` and `site_id` set (env or args). Prefer a single script that runs the full flow so the user can approve once.
- **Idempotency**: If the user might re-run, check for existing collections by name before creating; optionally skip or error if "Categories", "Tags", "Authors", or "Blog Posts" already exist.

## Common issues

- **401 Unauthorized**: Token missing, expired, or wrong scope. Ensure `cms:read` and `cms:write`. Do not hardcode the token.
- **409 Conflict / name in use**: A collection with that display name or slug already exists. List collections first and avoid duplicates.
- **Reference field fails**: Ensure the referenced collection was created in the same run and use its exact `id` in `metadata.collectionId`.
- **Missing SEO on taxonomies**: Categories, Tags, and Authors must each have Meta Title, Meta Description, and OG Image; do not add SEO only to Blog Posts.

## Notes

- Creates 4 collections total; confirm the site plan allows it.
- Order matters: create Categories, Tags, and Authors before Blog Posts; add Blog Posts reference fields after the collection exists.
- Field slugs are derived from display names by the API (e.g. "Meta Title" → "meta-title"). You do not send `slug` for fields in the create-collection payload unless the API explicitly supports it.
- [Webflow CMS API reference](https://developers.webflow.com/data/v2.0.0/reference/cms) and [field types](https://developers.webflow.com/data/v2.0.0/reference/field-types-item-values) for exact types and metadata shapes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/8020admin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
