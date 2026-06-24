---
name: idea-storer
description: Integrate with IDEA Storer CLI to read, manage, and act on stored development ideas. Use when user mentions "ideas", "IS", "idea storer", "check my ideas", "what should I work on", or asks about brainstorming and task planning. Use when this capability is needed.
metadata:
  author: byigitt
---

# IDEA Storer (IS) Integration

A lightning-fast CLI tool for capturing development ideas that AI coding agents can understand and use.

## Quick Reference

| Command | Description |
|---------|-------------|
| `is "idea"` | Add idea instantly |
| `is list` | Show recent ideas |
| `is done <id>` | Mark as completed |
| `is search "query"` | Search ideas |

## Discovery

Check for ideas in this order:
1. **Project-specific**: `.ideas.json` in current directory or git root
2. **Global fallback**: `~/.config/ideastorer/ideas.json` (Linux/macOS) or `%APPDATA%\ideastorer\ideas.json` (Windows)

## Reading Ideas

When the user mentions ideas, tasks, or asks "what should I work on":

1. Read the `.ideas.json` file using available file reading tools
2. Parse the JSON and extract active ideas
3. Present relevant ideas based on current context
4. Suggest which ideas relate to current work

## JSON Schema

```json
{
  "version": "1.0.0",
  "ideas": [
    {
      "id": "abc12345",
      "content": "The idea text",
      "tags": ["tag1", "tag2"],
      "priority": "low|medium|high",
      "status": "active|done|archived",
      "created_at": "ISO8601",
      "updated_at": "ISO8601",
      "context": {
        "project": "project-name",
        "directory": "/path/to/project",
        "git_branch": "branch-name"
      }
    }
  ],
  "metadata": {
    "last_modified": "ISO8601",
    "idea_count": 1
  }
}
```

## CLI Commands

```bash
# Quick capture
is "my brilliant idea"           # Add idea instantly
is "fix bug" -t bug,urgent       # Add with tags
is "critical task" -p high       # Add with priority

# Viewing
is list                          # Show recent ideas
is list -n 10                    # Show 10 ideas
is list -t feature               # Filter by tag
is list --status done            # Filter by status
is list --all                    # Show all ideas

# Searching
is search "authentication"       # Search by content
is search "api" -t backend       # Search with tag filter

# Managing
is done abc123                   # Mark as completed (partial ID works)
is delete abc123                 # Remove idea
is delete abc123 --force         # Remove without confirmation

# Exporting
is export                        # Export as JSON
is export --format markdown      # Export as Markdown
is export -o backup.json         # Export to file

# Setup
is init                          # Initialize .ideas.json
is --global init                 # Initialize global storage
```

## Workflow Integration

### Before Starting Work
When user asks "What should I work on?" or "Check my ideas":
1. Read `.ideas.json` from project root
2. Filter for `status: "active"` ideas
3. Sort by priority (high → medium → low)
4. Present actionable items with their IDs

### During Implementation
- Reference idea IDs when discussing related tasks
- Cross-check implementation against stored ideas
- Suggest related ideas that could be addressed together
- When a task is complete, remind user to mark it done

### After Completion
When user completes a task related to an idea:
1. Identify the matching idea by ID or content
2. Suggest running: `is done <id>`
3. Confirm the idea was marked as completed

## Example Interactions

**User**: "What should I work on?"
```
→ Read .ideas.json
→ Filter active ideas
→ Sort by priority
→ Present: "Here are your high-priority ideas: ..."
```

**User**: "Check IS for auth ideas"
```
→ Search ideas containing "auth" or tagged "authentication"
→ Present matching ideas with context
```

**User**: "I just finished the login feature"
```
→ Find related idea about login
→ Suggest: "Should I mark idea abc123 as done? Run: is done abc123"
```

**User**: "Add to IS: implement dark mode"
```
→ Run: is add "implement dark mode" -t ui,feature
→ Confirm idea was added
```

## Installation

Install IDEA Storer CLI:

```bash
# From crates.io
cargo install ideastorer

# Or download binary from releases
# https://github.com/byigitt/ideastorer/releases
```

## Tips

- Use short IDs (first 4+ characters) - `is done abc1` works if unique
- Tags help organize: `-t bug,urgent` or `-t feature,backend`
- Priority levels: `low`, `medium` (default), `high`
- Use `--json` flag for machine-readable output
- Global storage (`--global`) for cross-project ideas

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/byigitt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
