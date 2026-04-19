---
name: jira
description: Work with Jira issues using the jira CLI. Use for listing, creating, viewing, editing, deleting, cloning, and linking Jira issues. Supports CRUD operations, custom fields, JQL queries, epics, sprints, and common workflows like finding your assigned issues, filtering by status, and bulk operations. Use when this capability is needed.
metadata:
  author: thomascountz
---

# Jira CLI

Work with Jira issues efficiently using the `jira` CLI.

## Quick Start

List your assigned issues:

```bash
jira issue list -a$(jira me) --plain
```

## Core Workflows

### List Issues

Find issues with various filters:

```bash
# Your open issues
jira issue list -a$(jira me) -s~Resolved --plain

# High priority bugs
jira issue list -tBug -yHigh

# Created this week
jira issue list --created week

# With specific labels
jira issue list -lbackend -lcritical

# Raw JQL query
jira issue list -q"project = PROJ AND sprint in openSprints()"
```

### Create Issue

Create new issues with required and optional fields:

```bash
# Interactive mode (prompts for fields)
jira issue create

# Quick creation with flags
jira issue create -tBug -s"Login fails on mobile" -yHigh -b"Users report..."

# With labels and components
jira issue create -tStory -s"Add dark mode" -lux -lfrontend -CFrontend

# Attach task to an epic
jira issue create -tTask -PEPIC-123 -s"Implement feature X"

# Attach story to an epic
jira issue create -tStory -PEPIC-123 -s"User can do Y"

# Sub-task (requires parent)
jira issue create -t"Sub-task" -PISSUE-123 -s"Write tests"
```

#### Using Files for Descriptions

Prefer writing descriptions to a file and piping via stdin over inline `-b` strings, especially for multi-line or complex content. Piping works consistently for both `create` and `edit`.

```bash
# Write description to a file, then pipe it
echo "Description content here" > /tmp/desc.md
cat /tmp/desc.md | jira issue create -tStory -s"Title" --no-input
cat /tmp/desc.md | jira issue edit ISSUE-1 --no-input
```

This is recommended because:
- The user can inspect and tweak the file before you run the command
- Avoids shell quoting issues with special characters in `-b`
- Easier to debug when formatting doesn't render as expected in Jira

### View Issue

Display issue details:

```bash
# Interactive view
jira issue view ISSUE-1

# Plain text output
jira issue view ISSUE-1 --plain

# Show 5 comments
jira issue view ISSUE-1 --comments 5

# Raw JSON (useful for custom fields)
jira issue view ISSUE-1 --raw
```

### Edit Issue

Update existing issues:

```bash
# Interactive edit
jira issue edit ISSUE-1

# Quick updates
jira issue edit ISSUE-1 -yHigh -s"Updated title"

# Add/remove labels (use minus to remove)
jira issue edit ISSUE-1 -lurgent -l-wontfix

# Reassign
jira issue edit ISSUE-1 -ajohn.doe@company.com
```

### Custom Fields

Work with custom fields (see [references/custom-fields.md](references/custom-fields.md) for details):

```bash
# Set custom fields on create
jira issue create -tStory -s"Title" --custom story-points=5

# Update custom fields
jira issue edit ISSUE-1 --custom severity=Critical --custom "found in version"="2.1.0"
```

## Other Operations

### Transitions

```bash
# Move issue to different status
jira issue move ISSUE-1

# With specific transition
jira issue move ISSUE-1 "In Progress"
```

### Comments

```bash
# Add comment
jira issue comment add ISSUE-1 "This is my comment"

# List comments
jira issue comment list ISSUE-1
```

### Assignment

```bash
# Assign to user
jira issue assign ISSUE-1 jane.doe@company.com

# Assign to yourself
jira issue assign ISSUE-1 $(jira me)

# Unassign
jira issue assign ISSUE-1 x
```

### Clone Issue

Clone an existing issue, optionally overriding fields:

```bash
# Basic clone
jira issue clone ISSUE-1

# Clone with overrides
jira issue clone ISSUE-1 -s"Cloned summary" -yHigh -a$(jira me)

# Find/replace in summary and description (case-sensitive)
jira issue clone ISSUE-1 -H"old text:new text"
```

### Link Issues

Connect related issues or add web links:

```bash
# Link two issues (interactive - prompts for link type)
jira issue link ISSUE-1 ISSUE-2

# With specific link type
jira issue link ISSUE-1 ISSUE-2 Blocks

# Add a remote web link
jira issue link remote ISSUE-1 https://example.com "Link title"
```

### Delete

```bash
# Prompts for confirmation
jira issue delete ISSUE-1

# Non-interactive (--no-input not supported)
echo "y" | jira issue delete ISSUE-1

# Delete with all subtasks
echo "y" | jira issue delete ISSUE-1 --cascade
```

### Epics

```bash
# List issues in an epic
jira epic list EPIC-1

# Create an epic (-n sets the epic name)
jira epic create -n"Epic Name" -s"Epic summary"

# Add issues to an epic (up to 50)
jira epic add EPIC-1 ISSUE-1 ISSUE-2 ISSUE-3

# Remove issues from an epic
jira epic remove ISSUE-1 ISSUE-2
```

### Sprints

```bash
# List sprints (current board)
jira sprint list

# List issues in a specific sprint
jira sprint list SPRINT_ID

# Current/previous/next sprint shortcuts
jira sprint list --current
jira sprint list --prev
jira sprint list --next

# Add issues to a sprint (up to 50)
jira sprint add SPRINT_ID ISSUE-1 ISSUE-2
```

## Output Formats

Control output format based on use case:

```bash
# Interactive list (default)
jira issue list

# Plain table
jira issue list --plain

# Specific columns
jira issue list --plain --columns KEY,SUMMARY,STATUS,ASSIGNEE

# CSV export
jira issue list --csv > issues.csv

# Raw JSON
jira issue list --raw
```

## Useful Patterns

### Filter Combinations

```bash
# Your in-progress work
jira issue list -a$(jira me) -s"In Progress" --plain

# Unassigned high-priority bugs
jira issue list -tBug -yHigh -ax --plain

# Recently updated (last 2 days)
jira issue list --updated -2d

# Exclude resolved/closed
jira issue list -s~Resolved -s~Closed
```

### Bulk Operations

```bash
# List issues as JSON and process
jira issue list -a$(jira me) --raw | jq -r '.issues[].key' | while read key; do
    echo "Processing $key"
    jira issue edit "$key" -lprocessed --no-input
done
```

## Known Issues

### Formatting Problems in Descriptions and Comments

The jira CLI has several open bugs around text formatting:

- **Markdown conversion inconsistency:** `jira issue create -b` converts GitHub-flavored markdown to Jira format, but `jira issue edit -b` may not ([#935](https://github.com/ankitpokhrel/jira-cli/issues/935), [#549](https://github.com/ankitpokhrel/jira-cli/issues/549)). Behavior varies between Jira Cloud and on-prem.
- **Backslash escaping:** Special characters like dashes, underscores, and parentheses can get backslash-escaped in comments and descriptions, corrupting content ([#843](https://github.com/ankitpokhrel/jira-cli/issues/843)). E.g., `ISSUE-123` may render as `ISSUE\-123`.
- **No checkbox/Action Item support:** Jira Cloud's Action Items require ADF `taskList` nodes via the v3 REST API. The CLI's markdown conversion doesn't produce these, so `- [ ]` / `- [x]` won't render as checkboxes. Avoid using checkboxes in descriptions and comments; use plain bullet lists instead.

**Mitigation:** Pipe descriptions from a file (`cat desc.md | jira issue ...`) instead of inline `-b` strings. This lets you inspect the content before submission and makes it easier to iterate if formatting is off. If problems persist, check the rendered output in Jira and adjust the source file.

## Tips

- Prefer piping from a file (`cat desc.md | jira issue ...`) over `-b"inline"` for descriptions — easier to review, debug, and edit
- Use `$(jira me)` to reference current user
- Use `~` prefix to exclude values (e.g., `-s~Done`)
- Use `x` as assignee to indicate "unassigned"
- Use `-P` to attach issues to epics or create sub-tasks
- Add `--plain` for scriptable output
- Add `--no-input` to skip interactive prompts — but not all commands support it. Notable exceptions:
  - `jira issue delete` — pipe `echo "y"` for confirmation instead
  - `jira issue link remote` — runs non-interactively by default, no flag needed
- Check exit codes for automation ($? = 0 for success)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thomascountz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
