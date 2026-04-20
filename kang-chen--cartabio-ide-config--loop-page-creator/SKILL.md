---
name: loop-page-creator
description: Creates Loop Page documents following company SOP standards. Use when user wants to create a new Loop page, meeting record, project document, or any documentation following company's Loop workspace conventions. Triggers on keywords like "Loop", "SOP", "create document", "meeting record", "project doc", or when user needs team documentation.
metadata:
  author: kang-chen
---

# Loop Page Creator

Create documents for Microsoft Loop workspace following company standards.

## Quick Start

1. Determine document type from user request
2. Load appropriate template from `assets/templates/`
3. Fill in content based on user input
4. Output markdown with Loop command markers

## Document Types and Templates

| Type | Tag | Template | Use Case |
|------|-----|----------|----------|
| General Doc | `[Doc]` | `assets/templates/doc-template.md` | Architecture, guides, knowledge |
| Procedure | `[SOP]` | `assets/templates/sop-template.md` | Step-by-step operations |
| Event Record | `[Log]` | `assets/templates/log-template.md` | Migrations, incidents, meetings |
| Reference | `[Ref]` | Use doc-template | Dictionaries, error codes |
| Work in Progress | `[Draft]` | Any template | Incomplete documents |

## Workflow

### Step 1: Identify Requirements

From user input, determine:
- **Document type** → Select template
- **Target location** → Module/Entity/Perspective structure
- **Content** → Abstract, body, metadata

### Step 2: Generate Content

Load template and replace placeholders:

```markdown
> 💡 Abstract
>
> [Generated summary based on user input]

<!-- LOOP: Type /Table of contents here -->

# 1. Context & Goal
[Generated content...]
```

### Step 3: Output with Loop Markers

Include `<!-- LOOP: command -->` markers for features requiring manual Loop commands:
- TOC: `<!-- LOOP: Type /Table of contents here -->`
- Status: `<!-- LOOP: /Label → Doc Status → [status] -->`
- Maintainer: `<!-- LOOP: @name -->`
- Date: `<!-- LOOP: /Date -->`

## Loop Paste Instructions

Include these instructions when outputting documents:

> **To paste in Loop:**
> 1. Copy the markdown content below
> 2. In Loop, press `Ctrl+Shift+V` (Win) or `Cmd+Shift+V` (Mac)
> 3. Replace `<!-- LOOP: ... -->` markers with actual Loop commands

## References

- **Loop compatibility details**: See `references/loop-compatibility.md`
- **Full company guidelines**: See `references/company-guidelines.md`
- **Workspace structure**: See `knowledge/SOP/Loop.md`

## Page Title Format

Generate title as: `[Tag] Descriptive Title`

Examples:
- `[Doc] LaminDB Architecture Overview`
- `[SOP] Database Backup Procedure`
- `[Log] 2026-01-19 Migration Record`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kang-chen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
