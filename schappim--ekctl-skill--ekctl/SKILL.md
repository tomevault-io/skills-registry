---
name: ekctl
description: Manage macOS Calendar events and Reminders using the ekctl CLI tool Use when this capability is needed.
metadata:
  author: schappim
---

# ekctl - macOS Calendar & Reminders CLI

Use the `ekctl` command-line tool to manage Calendar events and Reminders on macOS. All output is JSON for easy parsing.

## Prerequisites

Ensure ekctl is installed:
```bash
brew tap schappim/ekctl && brew install ekctl
```

If not installed, guide the user to install it first.

## Workflow

### 1. Check for aliases first

Before any calendar/reminder operation, check if the user has aliases configured:
```bash
ekctl alias list
```

If aliases exist, use them instead of raw IDs. If no aliases exist, help the user set them up after listing calendars.

### 2. List available calendars

```bash
ekctl list calendars
```

This returns both event calendars (`type: "event"`) and reminder lists (`type: "reminder"`).

### 3. Set up aliases for convenience

Help users create aliases for frequently used calendars:
```bash
ekctl alias set work "CALENDAR_ID"
ekctl alias set personal "CALENDAR_ID"
ekctl alias set groceries "REMINDER_LIST_ID"
```

### 4. Perform requested operations

Use the appropriate command based on what the user wants to do. See `command-reference.md` for all commands.

## Date Format

**Always use ISO 8601 format with timezone:**

| Example | Description |
|---------|-------------|
| `2026-01-15T09:00:00Z` | 9:00 AM UTC |
| `2026-01-15T09:00:00+10:00` | 9:00 AM AEST |
| `2026-01-15T00:00:00Z` | Start of day (midnight UTC) |
| `2026-01-15T23:59:59Z` | End of day |

**Generate dates dynamically when needed:**
```bash
# Today at midnight UTC
TODAY=$(date -u +"%Y-%m-%dT00:00:00Z")

# Tomorrow at midnight UTC
TOMORROW=$(date -u -v+1d +"%Y-%m-%dT00:00:00Z")

# Next week
NEXT_WEEK=$(date -u -v+7d +"%Y-%m-%dT00:00:00Z")

# Specific time today (e.g., 2pm)
TODAY_2PM=$(date -u +"%Y-%m-%dT14:00:00Z")
```

## Common Patterns

### List today's events
```bash
TODAY=$(date -u +"%Y-%m-%dT00:00:00Z")
TOMORROW=$(date -u -v+1d +"%Y-%m-%dT00:00:00Z")
ekctl list events --calendar work --from "$TODAY" --to "$TOMORROW"
```

### List this week's events
```bash
TODAY=$(date -u +"%Y-%m-%dT00:00:00Z")
NEXT_WEEK=$(date -u -v+7d +"%Y-%m-%dT23:59:59Z")
ekctl list events --calendar work --from "$TODAY" --to "$NEXT_WEEK"
```

### Create a meeting
```bash
ekctl add event \
  --calendar work \
  --title "Team Standup" \
  --start "2026-01-15T09:00:00Z" \
  --end "2026-01-15T09:30:00Z" \
  --location "Conference Room A" \
  --notes "Weekly sync"
```

### Create an all-day event
```bash
ekctl add event \
  --calendar personal \
  --title "Vacation Day" \
  --start "2026-01-20T00:00:00Z" \
  --end "2026-01-21T00:00:00Z" \
  --all-day
```

### Add a reminder with due date
```bash
ekctl add reminder \
  --list personal \
  --title "Call the dentist" \
  --due "2026-01-16T10:00:00Z" \
  --priority 1
```

### List incomplete reminders
```bash
ekctl list reminders --list personal --completed false
```

### Complete a reminder
```bash
ekctl complete reminder "REMINDER_ID"
```

## JSON Output Handling

All ekctl commands return JSON. Use `jq` for parsing:

```bash
# Get calendar ID by name
WORK_ID=$(ekctl list calendars | jq -r '.calendars[] | select(.title == "Work") | .id')

# Count events
ekctl list events --calendar work --from "$TODAY" --to "$TOMORROW" | jq '.count'

# Get event titles only
ekctl list events --calendar work --from "$TODAY" --to "$TOMORROW" | jq -r '.events[].title'

# Export to CSV
ekctl list events --calendar work --from "2026-01-01T00:00:00Z" --to "2026-12-31T23:59:59Z" \
  | jq -r '.events[] | [.title, .startDate, .endDate, .location // ""] | @csv'
```

## Error Handling

Check the `status` field in responses:
```json
{
  "status": "error",
  "error": "Calendar not found with ID: invalid-id"
}
```

Common errors:
- **Permission denied**: User needs to grant Calendar/Reminders access in System Settings
- **Calendar not found**: Invalid calendar ID or alias - run `ekctl list calendars`
- **Invalid date format**: Must use ISO 8601 format

## Anti-patterns

- ❌ Using raw calendar IDs repeatedly without suggesting aliases
- ✅ Help users set up aliases for calendars they use often

- ❌ Hardcoding dates without explaining the format
- ✅ Show the ISO 8601 format and generate dates dynamically when appropriate

- ❌ Ignoring the JSON status field
- ✅ Always check `status` field and handle errors gracefully

- ❌ Listing all events without a reasonable date range
- ✅ Use sensible date ranges (today, this week, this month)

## References

- See `command-reference.md` for complete command documentation
- See `scripting-examples.md` for advanced automation patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/schappim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
