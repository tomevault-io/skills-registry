---
name: new-doc
description: Create a new documentation page in the docs directory with proper MDX format, category placement, and sidebar integration Use when this capability is needed.
metadata:
  author: ngtanthanh-qc
---

# Create a New Documentation Page

Create a new documentation page in the `/docs/` directory.

## Existing Doc Categories

| Folder | Category |
|--------|----------|
| `01-test-automation/` | Test Automation (API testing, Web UI testing) |
| `02-ai-ml-agents/` | AI, ML & Agents (MCP servers, etc.) |
| `03-tools-technologies/` | Tools & Technologies (Programming languages) |
| `04-cicd-devops/` | CI/CD & DevOps (Jenkins, etc.) |
| `05-networking/` | Networking (pyATS, 802.1X, CCNA, SDA) |
| `06-resources-learning/` | Resources & Learning (Docusaurus, guides) |

## Arguments

- `$ARGUMENTS[0]`: Category number or name (e.g., "01" or "test-automation")
- `$ARGUMENTS[1]`: Document title

## File Naming

- Use kebab-case: `my-document-title.mdx`
- Place in the appropriate subcategory folder or create one if needed

## Frontmatter Template

```mdx
---
title: <Title>
sidebar_position: <auto-detect next position>
description: <Brief description for SEO>
---
```

## Content Structure

1. Start with an introduction explaining the topic
2. Use clear heading hierarchy (`##`, `###`, `####`)
3. Include code examples with proper language identifiers
4. Use admonitions for tips, warnings, notes: `:::tip`, `:::warning`, `:::note`
5. Add Mermaid diagrams for architecture/flows when appropriate
6. Include images in a local `img/` subfolder when needed

## If Creating a New Subcategory

Create a `_category_.json` file:
```json
{
  "label": "<emoji> Category Name",
  "position": <number>,
  "description": "Description of this category",
  "collapsible": true,
  "collapsed": false
}
```

After creating, suggest running `npm start` to preview the doc page.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ngtanthanh-qc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
