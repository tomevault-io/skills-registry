---
name: notion-cli
description: Notion workspace management via CLI. Activate when user mentions "Notion", "notion page", "notion database", or wants to search/create/update content in their Notion workspace. Use when this capability is needed.
metadata:
  author: zenoxzx
---

# Notion CLI Skill

Use this skill when the user wants to interact with their Notion workspace.

## Trigger Examples
- "Search my Notion for meeting notes"
- "Create a new Notion page"
- "Add content to my Notion page"
- "Query my Notion database"
- "Show me my Notion page"
- "Update my Notion page title"
- "Add a task to my Notion database"
- "Export my Notion page to markdown"
- "Backup my Notion workspace"

## Quick Reference

### Authentication
```bash
notion-cli --set-auth <integration-token>
notion-cli --set-auth <token> --profile work   # Named profile
notion-cli --check-auth
notion-cli --clear-auth
notion-cli --list-profiles
```

### Pages
```bash
notion-cli --get-page <page-id>
notion-cli --create-page <parent-id> --title "Title" [--icon "emoji"] [--template <name>]
notion-cli --update-page <page-id> --title "New Title"
notion-cli --archive-page <page-id>
```

### Blocks (Page Content)
```bash
notion-cli --get-blocks <page-id> [--all]
notion-cli --get-block <block-id>
notion-cli --append-block <page-id> --type paragraph --content "Text"
notion-cli --append-blocks <page-id> --file blocks.json
notion-cli --update-block <block-id> --content "New text"
notion-cli --delete-block <block-id>
```

Block types: `paragraph`, `heading_1`, `heading_2`, `heading_3`, `bulleted_list_item`, `numbered_list_item`, `to_do`, `quote`, `callout`, `toggle`, `divider`, `code`

### Databases
```bash
notion-cli --get-database <db-id>
notion-cli --query-database <db-id> [--filter <json>] [--sort <json>] [--all]
notion-cli --create-db-item <db-id> --props '<json>'
notion-cli --update-db-item <item-id> --props '<json>'
```

### Database Shortcuts (Task Management)
```bash
notion-cli --add-task <db-id> --name "Task Name" [--status "Status"] [--due "2024-12-31"]
notion-cli --complete-task <task-id>
notion-cli --list-tasks <db-id> [--status "In Progress"]
```

### Search
```bash
notion-cli --search "query" [--filter page|database] [--all]
```

### Comments
```bash
notion-cli --get-comments <block-id>
notion-cli --add-comment <page-id> --content "Comment text" [--discussion <id>]
```

### Users
```bash
notion-cli --get-users
notion-cli --get-user <user-id>
notion-cli --get-me
```

### Templates
```bash
notion-cli --list-templates
notion-cli --create-page <parent-id> --template meeting-notes --title "Title"
```

Available: `meeting-notes`, `task-list`, `project-brief`, `weekly-review`, `bug-report`

### Export & Import
```bash
notion-cli --export-page <page-id> [--export-format markdown|json] [--output <file>]
notion-cli --import-markdown <page-id> --file <file.md>
notion-cli --backup [--output <file.json>]
```

### Output Formats
```bash
notion-cli <command> --format json|table|csv|yaml|pretty
```

## Workflows

### 1. First-time Setup
```bash
# User needs to create an integration at https://www.notion.so/my-integrations
# Then share pages/databases with the integration
notion-cli --set-auth secret_xxxxx
notion-cli --check-auth
```

### 2. Search and View Content
```bash
notion-cli --search "meeting notes"
# Get page ID from results
notion-cli --get-page <page-id>
notion-cli --get-blocks <page-id> --all
```

### 3. Create New Page with Template
```bash
notion-cli --create-page <parent-id> --template meeting-notes --title "Weekly Sync"
# Get new page ID from response, then add more content
notion-cli --append-block <new-page-id> --type paragraph --content "Additional notes..."
```

### 4. Quick Task Management
```bash
# Add a new task
notion-cli --add-task <db-id> --name "Review PR" --status "In Progress" --due "2024-12-31"

# List tasks by status
notion-cli --list-tasks <db-id> --status "In Progress"

# Complete a task
notion-cli --complete-task <task-id>
```

### 5. Export and Backup
```bash
# Export single page to markdown
notion-cli --export-page <page-id> --export-format markdown --output page.md

# Backup entire workspace
notion-cli --backup --output workspace-backup.json
```

### 6. Multiple Workspaces
```bash
# Set up profiles for different workspaces
notion-cli --set-auth secret_personal --profile personal
notion-cli --set-auth secret_work --profile work

# Use specific profile
notion-cli --search "notes" --profile work
```

## Response Format
All responses are JSON:
```json
{"ok":true,"data":{...}}
{"ok":false,"error":"message","code":"ERROR_CODE"}
```

## Error Codes
- `AUTH_ERROR` - Authentication failed
- `NOT_FOUND` - Resource not found
- `RATE_LIMIT` - Too many requests
- `MISSING_PARAM` - Required parameter missing
- `INVALID_PARAM` - Invalid parameter value
- `HTTP_ERROR` - Network error

## Important Notes
1. The integration must be shared with pages/databases to access them
2. Page IDs can be found in the page URL (32-character string after the page name)
3. Use `--database` flag when creating a page inside a database
4. Filter and props parameters must be valid JSON
5. Use `--all` flag to auto-paginate and get all results
6. Use `--format table` for human-readable output

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zenoxzx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
