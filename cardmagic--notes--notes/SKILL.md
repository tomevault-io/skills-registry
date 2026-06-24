---
name: notes
description: Search, browse, create, and delete Apple Notes. Use when user asks about notes, wants to find something in notes, create a new note, or delete notes. Use when this capability is needed.
metadata:
  author: cardmagic
---

# Apple Notes Skill

Search, browse, create, and delete Apple Notes with fuzzy matching.

## Prerequisites

Ensure the notes CLI is installed:

```bash
if ! command -v notes &> /dev/null; then
  pnpm add -g @cardmagic/notes
fi
```

## Commands

### Search notes
```bash
notes search "query" [-l limit] [-f folder] [-a after_date]
```

### Recent notes
```bash
notes recent [-l limit]
```

### Read a note by ID
```bash
notes read <id>
```

### List folders
```bash
notes folders [-l limit]
```

### Notes in folder
```bash
notes folder "folder_name" [-l limit]
```

### Index statistics
```bash
notes stats
```

### Rebuild index
```bash
notes index [-f|--force]
```

### Create a note
```bash
notes create "Title" --body "Content" [--folder "Folder"]
```

### Delete a note
```bash
notes delete "Title" [--folder "Folder"]
```

## Examples

- Search for "recipe": `notes search "recipe"`
- Recent notes: `notes recent`
- Notes in Taxes folder: `notes folder Taxes`
- Read note ID 123: `notes read 123`
- Create a note: `notes create "Meeting Notes" --body "Agenda items..."`
- Delete a note: `notes delete "Old Draft"`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cardmagic) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
