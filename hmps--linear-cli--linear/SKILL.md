---
name: linear
description: Interact with Linear issue tracker via CLI. Use when working with Linear issues, projects, teams, cycles, or comments. Triggers on "linear issue", "create issue", "update issue status", "linear search", or any Linear-related task. Use when this capability is needed.
metadata:
  author: hmps
---

# Linear CLI

CLI for AI agents to interact with Linear issue tracker. All output is JSON for machine parsing.

## Prerequisites

**Check installation:**
```bash
which linear
```

If not found, inform user the CLI is not installed. They can install via:
```bash
bun install -g linear-cli
```

**Environment:** Requires `LINEAR_API_KEY` env var. If commands fail with auth error, remind user to set it.

## Configuration

Check `.claude/CLAUDE.md` for a `## Linear Settings` section with default team:

```markdown
## Linear Settings
- Default team: VAAM
```

If not present and the user frequently works with a specific team, ask if they want you to add it to `.claude/CLAUDE.md`. Use the default team when `--team` is not specified.

## Quick Reference

Run `linear --help` or `linear <command> --help` for full options.

### Core Commands

| Command | Description |
|---------|-------------|
| `linear whoami` | Current user info |
| `linear teams` | List all teams |
| `linear team fetch <key>` | Team details by key |
| `linear issue list [options]` | List issues (--team, --cycle, --project, --labels, --assignee, --limit) |
| `linear issue fetch <id>` | View issue details |
| `linear issue create <title> [options]` | Create issue (--team, --description, --assignee, --priority) |
| `linear issue update <id> [options]` | Update issue (--title, --description, --state, --assignee) |
| `linear issue status <id> <state>` | Change issue status (state name or ID) |
| `linear issue search <query>` | Search issues in team |
| `linear search issues <query>` | Global issue search |
| `linear comment list <issue-id>` | List issue comments |
| `linear comment create <issue-id> <body>` | Add comment |
| `linear cycle list [--team <key>] [--active]` | List cycles |
| `linear labels list [--team <key>]` | List labels |
| `linear user list [--team <key>]` | List users |

### Typical Workflows

**Find and update issue:**
```bash
linear search issues "bug in auth"
linear issue fetch ABC-123
linear issue update ABC-123 --state "In Progress"
linear comment create ABC-123 "Started investigation"
```

**Create issue with details:**
```bash
linear issue create "Fix login timeout" --team ENG --priority high --description "Users report..."
```

**Check team's active sprint:**
```bash
linear cycle list --team ENG --active
linear issue list --team ENG --cycle <cycle-id>
```

**Find issues by label:**
```bash
linear labels list --team ENG
linear issue list --team ENG --labels <label-id>
```

## Notes

- Issue IDs can be UUID or identifier (e.g., `ABC-123`)
- Priority levels: `urgent`, `high`, `medium`, `low`
- State names are team-specific; use `linear team fetch <key>` to see available states
- All list commands support `--limit` and pagination via `--after <cursor>`

## Important: Search vs List

- **`issue search`** requires a real query term. Empty strings or wildcards (`*`) won't work.
- **`issue list`** is better for filtering by label/assignee without a search term.
- Labels and assignees require IDs, not names. Get IDs first:
  ```bash
  linear labels list --team VAAM  # Find label ID
  linear user list --team VAAM    # Find user ID
  linear issue list --team VAAM --labels <label-id> --assignee <user-id>
  ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hmps) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
