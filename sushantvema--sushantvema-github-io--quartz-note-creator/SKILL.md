---
name: quartz-note-creator
description: | Use when this capability is needed.
metadata:
  author: sushantvema
---

# Quartz Note Creator

Create new notes in `content/notes/` with proper Quartz frontmatter.

## Usage

Use the `scripts/create_note.py` script:

```bash
python3 scripts/create_note.py "Note Title" [options]
```

**Options:**
- `-d`, `--description`: Note description
- `-p`, `--publish`: Set `publish: true` (default: false)
- `-t`, `--tags`: Space-separated tags
- `-o`, `--output`: Output directory (default: content/notes/)

## Examples

**Basic unpublished note:**
```bash
python3 scripts/create_note.py "Machine Learning Basics"
```

**Published note with description and tags:**
```bash
python3 scripts/create_note.py "Kubernetes Guide" \
  -d "Notes on k8s deployment" \
  --publish \
  -t devops kubernetes cloud
```

**Draft note:**
```bash
python3 scripts/create_note.py "Weekend Plans" -t personal
```

## Generated Frontmatter

```yaml
---
title: "Note Title"
description: "Optional description"
author: "Sushant Vema"
date_created: 2026-01-17T11:14:47
date: 2026-01-17T11:14:47
publish: false
tags:
  - "tag1"
  - "tag2"
---
```

## Field Descriptions

| Field | Value | Notes |
|-------|-------|-------|
| `title` | User-provided | Required |
| `description` | User-provided or enriched | Optional. Can be enhanced with context beyond user's exact words |
| `author` | "Sushant Vema" | Always set |
| `date_created` | ISO timestamp | Auto-generated |
| `date` | ISO timestamp | Auto-generated |
| `publish` | true/false | Default: false |
| `tags` | List | Optional |

## Description Guidelines

The description field is flexible and can be enriched by the agent:

- **User provides description**: Use it as-is or enhance with additional context
- **User doesn't provide description**: Create a concise, informative description based on the topic
- **Enrichment encouraged**: Add technical context, use cases, or clarifying details that make the note more discoverable

**Example enrichments**:
- User: "Create a note about PyO3"
- Basic: "A way to wrap Rust logic into Python modules"
- Enriched: "Rust-Python interoperability library for creating native Python extensions in Rust, providing performance benefits and type safety"

## Filename Generation

- Title is slugified (lowercase, dashes instead of spaces)
- Example: "Machine Learning Basics" → `machine-learning-basics.md`
- Placed in `content/notes/` by default

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sushantvema) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
