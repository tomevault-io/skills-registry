---
name: deepwiki-docs
description: > Use when this capability is needed.
metadata:
  author: chenycl
---

# DeepWiki Docs

Fetch and index documentation from [DeepWiki](https://deepwiki.com) for any public GitHub repository. DeepWiki provides AI-generated, structured documentation for open-source projects including architecture diagrams, API references, and code explanations.

## Quick Start

### Method 1: Script (recommended for bulk operations)

```bash
# Get wiki table of contents
python /path/to/scripts/deepwiki_fetch.py structure <owner/repo>

# Get a specific page
python /path/to/scripts/deepwiki_fetch.py content <owner/repo> <page-slug>

# Export TOC as markdown
python /path/to/scripts/deepwiki_fetch.py export <owner/repo> --output toc.md

# Build full knowledge index (all pages in one document)
python /path/to/scripts/deepwiki_fetch.py index <owner/repo> --output docs.md
```

### Method 2: web_fetch (for quick lookups)

```
# Fetch repo overview + sidebar structure
web_fetch("https://deepwiki.com/{owner}/{repo}")

# Fetch a specific documentation page
web_fetch("https://deepwiki.com/{owner}/{repo}/{page-slug}")
```

Parse the returned HTML:
- Sidebar `<a>` links matching `/{owner}/{repo}/{slug}` → table of contents
- Main text content → documentation

## Workflow

### Step 1: Get Wiki Structure
Always start by fetching the repository's documentation structure to discover available pages.

Use the script:
```bash
python scripts/deepwiki_fetch.py structure owner/repo
```
Or use `web_fetch` on `https://deepwiki.com/{owner}/{repo}` and parse sidebar links.

### Step 2: Fetch Relevant Pages
Based on the user's question, select and fetch the most relevant page(s).

Page slugs follow the pattern: `{N}-{title}` or `{N.M}-{title}` (e.g., `4.1-fiber-architecture`).

### Step 3: Synthesize and Present
Summarize the fetched documentation content in context of the user's question. Always cite the DeepWiki source URL.

### Building a Knowledge Index
For comprehensive documentation needs, use the `index` command to create a single markdown document containing all wiki pages. This is useful for:
- Creating offline reference documents
- Building LLM context for coding tasks
- Generating documentation archives

```bash
python scripts/deepwiki_fetch.py index owner/repo --output repo_docs.md
```

## Page Slug Format

| Pattern | Example | Description |
|---------|---------|-------------|
| `{N}-{title}` | `1-overview` | Top-level section |
| `{N.M}-{title}` | `4.1-fiber-architecture` | Sub-section |
| `{N.M.K}-{title}` | `3.2.1-webpack-config` | Deep sub-section |

## Reference Files

- **API details**: See [references/api_reference.md](references/api_reference.md) for DeepWiki MCP server endpoints, URL patterns, and HTML parsing guidance
- **Usage examples**: See [references/usage_examples.md](references/usage_examples.md) for common workflow patterns

## Notes

- Only **public** GitHub repositories are available without authentication
- If a repo is not indexed, direct the user to https://deepwiki.com to request indexing
- The script uses only Python stdlib (no pip installs needed)
- For the DeepWiki MCP server (advanced): `https://mcp.deepwiki.com/mcp` (no auth, three tools: `read_wiki_structure`, `read_wiki_contents`, `ask_question`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chenycl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
