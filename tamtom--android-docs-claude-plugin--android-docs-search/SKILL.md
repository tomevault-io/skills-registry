---
name: android-docs-search
description: Search Android/Kotlin documentation offline using SQLite index for API lookups and Grep for content search. Use when the user asks about Android APIs, Compose components, AndroidX classes, Kotlin extensions, or needs to find documentation for Android development. Use when this capability is needed.
metadata:
  author: tamtom
---

# Android Docs Search

## Overview

Search the local Android/Kotlin documentation. The documentation must be installed separately (see plugin README).

**Default location:** `~/StudioProjects/android-docs-claude-plugin/docs/`

Two search methods available:
- **SQLite index** - Fast API name lookups (189,506+ indexed entries)
- **Grep/Read** - Full-text search in HTML documentation content

## When to Use Each Method

| Method | Use For |
|--------|---------|
| SQLite | Finding classes, functions, properties by name |
| Grep | Searching documentation content, deprecation notices, examples |

## SQLite Index Search

Database location: `~/StudioProjects/android-docs-claude-plugin/docs/android_api_index.db`

### Table Schema
```sql
CREATE TABLE api_index(id INTEGER PRIMARY KEY, name TEXT, type TEXT, path TEXT);
```

### Entry Types
Class, Constructor, Property, Package, Annotation, Function, Enum, Constant, Object, Interface, Method

### Search Examples

```bash
# Find by exact name
sqlite3 ~/StudioProjects/android-docs-claude-plugin/docs/android_api_index.db \
  "SELECT name, type, path FROM api_index WHERE name LIKE '%LazyColumn%';"

# Filter by type
sqlite3 ~/StudioProjects/android-docs-claude-plugin/docs/android_api_index.db \
  "SELECT name, type FROM api_index WHERE name LIKE '%Modifier%' AND type='Class';"

# Find all entries in a package
sqlite3 ~/StudioProjects/android-docs-claude-plugin/docs/android_api_index.db \
  "SELECT name, type FROM api_index WHERE name LIKE 'androidx.compose.material3%';"
```

## Content Search (Grep)

Documentation location: `~/StudioProjects/android-docs-claude-plugin/docs/Documents/developer.android.com/`

### Search Examples

Use the Grep tool to search HTML files:
```
pattern: "deprecated"
path: ~/StudioProjects/android-docs-claude-plugin/docs/Documents/
glob: "*.html"
```

Or get context around a match:
```
pattern: "LocalAutofillTree"
path: <file_path_from_sqlite>
output_mode: "content"
-A: 10
-B: 2
```

## Recommended Workflow

1. **Start with SQLite** to find the API and its file path
2. **Use Grep/Read** to examine the actual documentation content

### Example: Find deprecation info for an API

```bash
# Step 1: Find the file
sqlite3 ~/StudioProjects/android-docs-claude-plugin/docs/android_api_index.db \
  "SELECT name, path FROM api_index WHERE name LIKE '%LocalAutofillTree%';"

# Step 2: Read the deprecation notice from the file using Read or Grep tool
```

## Documentation Structure

```
~/StudioProjects/android-docs-claude-plugin/
├── docs/
│   ├── android_api_index.db      # SQLite index (189,506+ entries)
│   └── Documents/
│       └── developer.android.com/
│           └── reference/
│               ├── android/      # Java Android APIs
│               └── kotlin/       # Kotlin Android APIs
│                   └── androidx/ # AndroidX libraries
├── scripts/
│   ├── bulk_updater.py           # Sync new pages from developer.android.com
│   ├── download_and_process.py   # Download single pages
│   └── update_index.py           # Manage SQLite index
└── skills/
    └── android-docs-search/
        └── SKILL.md
```

## Keeping Docs Updated

Run from the `docs/` directory:

```bash
# Check for new pages
python3 ../scripts/bulk_updater.py discover
python3 ../scripts/bulk_updater.py diff

# Download new pages
python3 ../scripts/bulk_updater.py sync

# Rebuild search index
python3 ../scripts/update_index.py rebuild
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tamtom) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
