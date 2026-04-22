---
name: icon-replacement
description: >- Use when this capability is needed.
metadata:
  author: michael0520
---

Find and suggest replacement icons during migration from legacy to One-UI.

## Arguments

- `$ARGUMENTS` - Query or command:
  - `<old-icon-name>` - Find a replacement for a specific icon
  - `list` - Show all available icons
  - `search <keyword>` - Search icons by keyword
  - `category <name>` - Show icons by category (navigation, action, status, network, file, etc.)

## References

| Topic | Description | Reference |
|-------|-------------|-----------|
| Icon List | All available icons organized by category | [icon-list](references/icon-list.md) |
| Common Replacements | Legacy-to-new icon mapping table | [common-replacements](references/common-replacements.md) |

## Workflow

### For `<old-icon-name>` query:
1. Search for exact match in available icons
2. If not found, suggest closest alternatives based on semantic meaning, visual similarity, and common use cases
3. Provide 2-3 options with explanations

### For `search <keyword>`:
1. Search icon names containing the keyword
2. Return all matches grouped by category

### For `category <name>`:
1. Return all icons in that category
2. Categories: navigation, action, status, network, file, people, time, ui, trend, location, media, communication, play, mx, project, misc

## Output Format

### For replacement queries:

```
## Icon Replacement for `{old-icon}`

**Recommended:** `icon:{recommended}` - {reason}

**Alternatives:**
- `icon:{alt1}` - {reason1}
- `icon:{alt2}` - {reason2}

**Usage:**
```html
<mat-icon svgIcon="{recommended}"></mat-icon>
```
```

### For search/list:

```
## Icons matching "{keyword}"

### {Category 1}
- `icon:name1`
- `icon:name2`

### {Category 2}
- `icon:name3`
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/michael0520) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
