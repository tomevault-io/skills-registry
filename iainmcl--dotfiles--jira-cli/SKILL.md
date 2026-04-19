---
name: jira-cli
description: Fetch Jira ticket details using Atlassian CLI (acli). Use when asked to get ticket info, check status, or understand ticket context. Use when this capability is needed.
metadata:
  author: iainmcl
---

# Jira Access

Use the Atlassian CLI (`acli`) to fetch Jira ticket details from TravelPerk's Jira instance.

## View Ticket Details

```bash
acli jira workitem view <KEY>
```

Returns: key, type, summary, status, assignee, description

**With JSON output:**
```bash
acli jira workitem view <KEY> --json
```

**With specific fields:**
```bash
acli jira workitem view <KEY> --fields "summary,status,assignee,description,comment"
```

Available field options:
- `*all` - all fields
- `*navigable` - navigable fields
- Specific: `key`, `issuetype`, `summary`, `status`, `assignee`, `description`, `comment`, `priority`
- Exclude with minus: `-description`

## List Comments

```bash
acli jira workitem comment list <KEY>
```

## Search Tickets

```bash
acli jira workitem search --jql "<JQL_QUERY>"
```

**Examples:**
```bash
acli jira workitem search --jql "project = QLT AND status = 'In Progress'"
acli jira workitem search --jql "assignee = currentUser() AND status != Done" --limit 10
acli jira workitem search --jql "project = QLT" --fields "key,summary,status" --csv
```

## Open in Browser

```bash
acli jira workitem view <KEY> --web
```

## Example Usage

```bash
# Get ticket QLT-33079 details
acli jira workitem view QLT-33079

# Get full JSON for parsing
acli jira workitem view QLT-33079 --json

# Check comments on a ticket
acli jira workitem comment list QLT-33079

# Find my open tickets
acli jira workitem search --jql "assignee = currentUser() AND status != Done"
```

## Create Tickets

```bash
acli jira workitem create --project "APP" --type "Task" --summary "Title" --description "Plain text" --assignee "@me"
```

**Note:** Component is mandatory for APP project. Plain text descriptions won't have formatting.

## Edit Tickets

**Simple edits (summary only):**
```bash
acli jira workitem edit --key "APP-12345" --summary "New title" -y
```

**With formatted description (ADF JSON):**

Plain text descriptions via `--description` or `--description-file` will NOT render with proper formatting (no headings, code blocks, lists). To get proper formatting, you must use Atlassian Document Format (ADF) JSON via `--from-json`.

Create a JSON file with ADF structure:
```json
{
  "issues": ["APP-12345"],
  "description": {
    "type": "doc",
    "version": 1,
    "content": [
      {
        "type": "paragraph",
        "content": [{"type": "text", "text": "Intro paragraph"}]
      },
      {
        "type": "heading",
        "attrs": {"level": 2},
        "content": [{"type": "text", "text": "Section Title"}]
      },
      {
        "type": "paragraph",
        "content": [
          {"type": "text", "text": "Code: "},
          {"type": "text", "text": "inline_code", "marks": [{"type": "code"}]}
        ]
      },
      {
        "type": "codeBlock",
        "attrs": {"language": "python"},
        "content": [{"type": "text", "text": "def foo():\n    pass"}]
      },
      {
        "type": "bulletList",
        "content": [
          {"type": "listItem", "content": [{"type": "paragraph", "content": [{"type": "text", "text": "Item 1"}]}]},
          {"type": "listItem", "content": [{"type": "paragraph", "content": [{"type": "text", "text": "Item 2"}]}]}
        ]
      },
      {
        "type": "orderedList",
        "content": [
          {"type": "listItem", "content": [{"type": "paragraph", "content": [{"type": "text", "text": "Step 1"}]}]},
          {"type": "listItem", "content": [{"type": "paragraph", "content": [{"type": "text", "text": "Step 2"}]}]}
        ]
      }
    ]
  }
}
```

Then apply:
```bash
acli jira workitem edit --from-json /path/to/ticket.json -y
```

**ADF Node Types:**
- `paragraph` - regular text
- `heading` - with `attrs.level` (1-6)
- `codeBlock` - with optional `attrs.language`
- `bulletList` / `orderedList` - containing `listItem` nodes
- `listItem` - must contain a `paragraph`

**Text Marks:**
- `{"type": "code"}` - inline code
- `{"type": "strong"}` - bold
- `{"type": "em"}` - italic

## Authentication

If authentication fails, run:
```bash
acli jira auth
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iainmcl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
