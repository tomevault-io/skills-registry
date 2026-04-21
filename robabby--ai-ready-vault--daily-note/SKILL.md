---
name: daily-note
description: Work with Obsidian daily notes. Create today's note if missing, append content to existing note, or read current daily note. Uses Obsidian's periodic notes conventions. Requires Obsidian MCP server. Use when this capability is needed.
metadata:
  author: robabby
---

# Daily Note

Create, read, or append to daily notes.

## Workflow

### Read Today's Note
1. Use `obsidian_get_periodic_note({ period: "daily" })`
2. Display content or note if missing

### Create Today's Note
1. Check if daily note exists
2. Determine path from periodic notes config or CLAUDE.md
3. Create with appropriate template/frontmatter
4. Common path patterns:
   - `Planner/YYYY/MM-Month/YYYY-MM-DD.md`
   - `Daily/YYYY-MM-DD.md`
   - `Journal/YYYY/YYYY-MM-DD.md`

### Append to Daily Note
1. Get existing note content
2. Use `obsidian_append_content` to add new content
3. Respect existing structure (find appropriate section)

## MCP Tools Used

```typescript
// Get today's daily note
obsidian_get_periodic_note({ period: "daily" })

// Get recent daily notes
obsidian_get_recent_periodic_notes({
  period: "daily",
  limit: 5,
  include_content: true
})

// Append content
obsidian_append_content({
  filepath: "Planner/2025/01-January/2025-01-08.md",
  content: "\n## Notes\n- Added item"
})
```

## Daily Note Template

```markdown
---
created: {date}
type: daily
tags:
  - daily
  - planner
---

# {date}

## Plan
- [ ]

## Notes


## Log

```

## Parameters

- `$ARGUMENTS` (optional):
  - No args: Read today's note
  - `create`: Create today's note if missing
  - `{content}`: Append content to today's note

## Example

User: `/daily-note`

Response:
"Today's daily note (`Planner/2025/01-January/2025-01-08.md`):

---
## Plan
- [x] AI Ready Vault brainstorming session
- [ ] Review implementation plan

## Notes
- Evolved product concept from templates to skills library
- Created project documentation in vault

## Log
- 09:00 - Started brainstorming session
---"

User: `/daily-note Added meeting notes from standup`

Response:
"Appended to today's daily note:

Added to ## Notes section:
'- Added meeting notes from standup'

File: `Planner/2025/01-January/2025-01-08.md`"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/robabby) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
