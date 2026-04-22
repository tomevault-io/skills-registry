---
name: update-dashboard
description: This skill should be used when you need to refresh the Dashboard with current system status, pending actions, recent activity, and statistics. It automatically gathers data from action folders (Needs_Action, Plans, Done), checks watcher status, and updates the Dashboard.md file. Use this skill when the user asks to refresh/update the dashboard, check system status, or get an overview of pending tasks and recent activity. Use when this capability is needed.
metadata:
  author: ameen-alam
---

# Update Dashboard

Refresh the Dashboard with current system status and activity.

## Before Implementation

Gather context to ensure successful execution:

| Source | What to Gather |
|--------|----------------|
| **Project Structure** | Use Glob to verify directories exist: `Needs_Action/`, `Done/`, `Plans/`, and `Dashboard.md` |
| **Configuration** | Ask user about base directory path if not standard (default: `./AI_Employee_Vault`) |
| **Watcher Logs** | Identify watcher log location and format for status checking |
| **User Preferences** | Confirm display preferences (thresholds, item counts) on first use |

If critical directories are missing, ask user to confirm project structure before proceeding.

## Dependencies

**Required Claude Tools:**
- Read: For reading Dashboard.md and action files
- Glob: For finding and counting files in directories
- Bash: For checking timestamps and file operations
- Edit or Write: For updating Dashboard.md

**Required File System Access:**
- Read access: Action directories (`Needs_Action/`, `Done/`, `Plans/`)
- Write access: `Dashboard.md` file

**File Format Requirements:**
- Action files must have YAML frontmatter with `status`, `priority`, `detected` fields
- See [references/file-format-spec.md](references/file-format-spec.md) for complete specification

## Configuration

On first use or when user requests customization, clarify:

1. **Base directory**: Where is your AI Employee Vault? (default: `./AI_Employee_Vault`)
2. **Watcher status thresholds**:
   - Running: Last entry < X minutes ago (default: 5)
   - Slow: Last entry X-Y minutes ago (default: 5-15)
   - Stopped: Last entry > Y minutes ago (default: 15)
3. **Display preferences**:
   - Recent activity count: How many items to show? (default: 5)
   - Stats time ranges: Today, this week (default)

## Instructions

### 1. Validate Project Structure

**Before gathering data**, verify required directories exist:

```bash
# Check for required directories
ls [BASE_DIR]/Needs_Action/
ls [BASE_DIR]/Done/
ls [BASE_DIR]/Plans/
```

**If any directory is missing:**
- Report to user which directories are missing
- Ask if they want to create the missing directories
- Do not proceed with update until structure is valid

### 2. Gather Current Status

**a. Count Files in Each Folder**

Use Glob to count `.md` files:
- `[BASE_DIR]/Needs_Action/*.md` (pending actions)
- `[BASE_DIR]/Plans/*.md` (active plans)
- `[BASE_DIR]/Done/*.md` (completed tasks)

**b. Check Watcher Status**

Look for most recent log entry in today's log file:
- If last entry < 5 minutes ago (or configured threshold): 🟢 Running
- If last entry 5-15 minutes ago (or configured range): 🟡 Slow
- If last entry > 15 minutes ago (or configured threshold): 🔴 Stopped

**Error handling:**
- If log file doesn't exist: Show "🟡 Unknown" status
- If log format is unexpected: Use file modification time as fallback

### 3. Get Pending Actions List

Read all files in `[BASE_DIR]/Needs_Action/`:

For each file:
1. Read YAML frontmatter
2. Extract: filename, `priority`, `detected` timestamp
3. Only include files with `status: pending` or `status: in_progress`
4. Format as list item with emoji indicator:
   - 🔴 High priority
   - 🟡 Medium priority
   - 🟢 Low priority

**Error handling:**
- If frontmatter is missing: Skip file with warning, continue processing
- If priority is missing: Default to medium (🟡)
- If detected timestamp is malformed: Use file modification time

**Empty state:**
- If no pending actions: Display "No pending actions"

### 4. Get Recent Activity

Read all files in `[BASE_DIR]/Done/`:

1. Extract `completed` timestamp from frontmatter
2. Sort by completion timestamp (most recent first)
3. Take top 5 (or configured count) completed items
4. Format with completion timestamp and brief description

**Error handling:**
- If completed timestamp missing: Use file modification time
- If file has no title: Use filename as description

**Empty state:**
- If no recent activity: Display "No recent activity"

### 5. Calculate Today's Stats

Calculate three key metrics:

- **Files Processed Today**: Count log entries for today, or count Done files modified today
- **Active Tasks**: Count of pending + in_progress items from Needs_Action
- **Completed This Week**: Count Done folder items from past 7 days

### 6. Update Dashboard.md

Read current `[BASE_DIR]/Dashboard.md`:

1. If Dashboard.md doesn't exist, create from template in [assets/dashboard-template.md](assets/dashboard-template.md)
2. Update the frontmatter `last_updated` field to current UTC timestamp (ISO 8601 format)
3. Replace each section with fresh data:
   - System Status
   - Pending Actions
   - Recent Activity
   - Quick Stats

Use Edit tool for existing file, Write tool for new file.

## Dashboard Structure

The Dashboard.md should follow this structure (see [assets/dashboard-template.md](assets/dashboard-template.md)):

```markdown
---
last_updated: [ISO timestamp]
---

# AI Employee Dashboard

## System Status
- Watcher Status: [🟢 Running / 🟡 Slow / 🔴 Stopped]
- Last Check: [timestamp of most recent log entry]

## Pending Actions
[List of pending items with priorities, or "No pending actions"]

## Recent Activity
[List of last 5 completed items, or "No recent activity"]

## Quick Stats
- Files Processed Today: [count]
- Active Tasks: [count]
- Completed This Week: [count]
```

## Error Handling

Handle common error scenarios gracefully:

| Error Scenario | Response |
|----------------|----------|
| **Missing directory** | Report to user, offer to create, do not proceed until resolved |
| **Malformed markdown file** | Skip file with warning, continue processing others |
| **Missing frontmatter fields** | Use defaults (status=pending, priority=medium, timestamp=file mtime) |
| **Invalid timestamps** | Use file modification time as fallback |
| **Empty directories** | Show appropriate empty state message |
| **Dashboard.md missing** | Create new file from template |
| **One section fails** | Update other sections successfully, log error for failed section |

Always update `last_updated` timestamp even if some sections fail.

## Examples

### ✅ Good: Well-formed action file

```markdown
---
status: pending
priority: high
detected: 2026-01-10T14:23:01Z
title: "Process invoice for Client A"
---

# Invoice Processing Required

Details here...
```

### ❌ Bad: Missing required fields

```markdown
---
# No status or priority fields!
---

# Some Task

This will be skipped or treated with defaults.
```

### ✅ Good: Empty state handling

When no pending actions exist:

```markdown
## Pending Actions
No pending actions
```

Not:

```markdown
## Pending Actions
[blank]
```

## Anti-Patterns

Avoid these common mistakes:

❌ **Don't manually edit counts** - Always recalculate from source data
❌ **Don't cache watcher status** - Always check latest log entry
❌ **Don't skip validation** - Verify all directories before processing
❌ **Don't use relative timestamps** - Always use ISO 8601 format with UTC
❌ **Don't proceed on errors** - Gracefully handle and report issues
❌ **Don't hardcode paths** - Use configured base directory

## Success Criteria

After running this skill, verify:

- [ ] Dashboard.md has current `last_updated` timestamp
- [ ] All file counts are accurate (verify with Glob)
- [ ] Watcher status reflects latest activity
- [ ] Pending actions list matches Needs_Action/ contents
- [ ] Recent activity shows latest items from Done/
- [ ] No errors or warnings in execution log

## Advanced Topics

For detailed information, see:

- **Dashboard design principles**: [references/dashboard-best-practices.md](references/dashboard-best-practices.md)
- **File format specifications**: [references/file-format-spec.md](references/file-format-spec.md)

## Related Skills

- Use `process-needs-action` to process the pending items listed
- Use `complete-task` to move items to Done folder

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ameen-alam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
