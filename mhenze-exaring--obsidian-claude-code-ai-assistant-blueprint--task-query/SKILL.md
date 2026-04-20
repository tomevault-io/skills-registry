---
name: task-query
description: Answers questions about tasks in the Obsidian Vault - open tasks, completed tasks, overdue tasks, tasks by tags/topics. Uses Dataview TABLE queries via the REST API. Use when this capability is needed.
metadata:
  author: mhenze-exaring
---

# Task Query Skill

Efficiently answers questions about tasks in the Obsidian Vault via Dataview TABLE queries with `file.tasks`.

## When to Use This Skill

Activate on questions like:
- "What tasks are open?"
- "What did I complete today/this week?"
- "Show me overdue tasks"
- "What are my tasks about [topic]?"
- "Which tasks have tag X?"
- "What was the last completed task about Y?"
- "Which tasks are due this week?"

## Technical Background

### Why TABLE instead of TASK Query?

The Obsidian REST API supports **only TABLE queries**, not TASK queries:

```dataview
-- Does NOT work via REST API:
TASK WHERE !completed

-- Works via REST API:
TABLE file.tasks as "Tasks" WHERE length(file.tasks) > 0
```

### Task Data Structure

Each task in `file.tasks` contains:

| Field | Type | Description |
|-------|------|-------------|
| `text` | string | Task description (incl. emojis) |
| `completed` | boolean | Completed? |
| `status` | string | `"x"`, `" "`, `"-"` |
| `due` | datetime | Due date (📅) |
| `completion` | datetime | Completion date (✅) |
| `tags` | array | Tags in task (e.g., `["#topic"]`) |
| `path` | string | File path |
| `line` | number | Line number |
| `children` | array | Indented bullets under the task |
| `subtasks` | array | Alias for children |

### Comments and Context

Indented bullets under a task appear in `children`/`subtasks`:

```markdown
- [x] Main task completed ✅ 2025-12-09
  - Comment or note about task
  - Additional context
```

→ The comment is available in `children[].text`.

## Query Patterns

### Basic Query (Tool Call)

```
mcp__obsidian__search_vault
  query: "TABLE file.tasks as \"Tasks\" FROM \"\" WHERE ..."
  queryType: "dataview"
```

### Open Tasks (vault-wide)

```dataview
TABLE file.tasks as "Tasks"
WHERE any(file.tasks, (t) => !t.completed AND t.text != "")
LIMIT 50
```

### Open Tasks in Specific Folder

```dataview
TABLE file.tasks as "Tasks"
FROM "Daily"
WHERE any(file.tasks, (t) => !t.completed)
LIMIT 30
```

### Tasks by Topic/Keyword

```dataview
TABLE file.tasks as "Tasks"
WHERE any(file.tasks, (t) => contains(lower(t.text), "topic"))
```

**Case-insensitive search:** `contains(lower(t.text), "keyword")`

### Tasks by Tag

```dataview
TABLE file.tasks as "Tasks"
WHERE any(file.tasks, (t) => contains(t.tags, "#topic"))
```

### Completed Tasks (recently finished)

```dataview
TABLE file.tasks as "Tasks"
WHERE any(file.tasks, (t) => t.completed AND t.completion)
SORT file.mtime DESC
LIMIT 20
```

### Overdue Tasks

```dataview
TABLE file.tasks as "Tasks"
WHERE any(file.tasks, (t) => !t.completed AND t.due AND t.due < date(today))
```

### Tasks Due This Week

```dataview
TABLE file.tasks as "Tasks"
WHERE any(file.tasks, (t) => !t.completed AND t.due AND t.due >= date(today) AND t.due <= date(today) + dur(7 days))
```

### Combined Search (Keyword + Status)

```dataview
TABLE file.tasks as "Tasks"
WHERE any(file.tasks, (t) => t.completed AND contains(lower(t.text), "topic"))
```

## Workflow

### Step 1: Execute Query

```
mcp__obsidian__search_vault(
  query: "<dataview-query>",
  queryType: "dataview"
)
```

### Step 2: Filter Result (client-side)

The query returns **all tasks of the file**. Filter in response:

```python
# Pseudo-code for client filtering
for file in results:
    relevant_tasks = [
        t for t in file.tasks
        if t.completed and "topic" in t.text.lower()
    ]
```

### Step 3: Load Context if Needed

If the user needs more context:
1. Use `path` and `line` from the task
2. Load the file with `mcp__obsidian__get_vault_file`
3. Show surrounding lines or the entire section

### Step 4: Format Response

**Short answer (task list):**

| Task | Status | Due | File |
|------|--------|-----|------|
| Description | ✅/⬜ | Date | Link |

**Detailed answer (with context):**

```
## Task: [Description]
- **Status:** Completed on 2025-12-09
- **Due:** 2025-12-04
- **File:** [[Areas/path/to/file.md]]
- **Comments:**
  - Configuration completed
  - Requires further setup
```

## Example Requests and Queries

| User Request | Query |
|--------------|-------|
| "Open tasks" | `WHERE any(file.tasks, (t) => !t.completed AND t.text != "")` |
| "Topic X tasks" | `WHERE any(file.tasks, (t) => contains(lower(t.text), "topic x"))` |
| "Completed this week" | `WHERE any(file.tasks, (t) => t.completed AND t.completion >= date(today) - dur(7 days))` |
| "Overdue" | `WHERE any(file.tasks, (t) => !t.completed AND t.due < date(today))` |
| "Tasks with #daily tag" | `WHERE any(file.tasks, (t) => contains(t.tags, "#daily"))` |
| "Last completed about X" | Search + SORT + LIMIT 1 + client filter for newest completion |
| "Completed tasks with results" | Query + `children` evaluation (see below) |

## Extracting Results and Outcomes from Tasks

### Convention: Results as Indented Bullets

Tasks with documented outcome follow this pattern:

```markdown
- [x] Task description ✅ 2025-12-09
  - Result: Configuration completed
  - Next step: Review with team
```

### Workflow: "What results did I achieve on completed tasks?"

**Step 1: Query for completed tasks in time range**

```dataview
TABLE file.tasks as "Tasks"
WHERE any(file.tasks, (t) => t.completed AND t.completion >= date(today) - dur(7 days))
```

**Step 2: Client-side filtering and extraction**

For each task in result:
1. Check `completed == true` and `completion` in time range
2. Extract `text` as task description
3. Extract `children[].text` as results/comments
4. Ignore tasks without `children` (no documented result)

**Step 3: Format response**

```markdown
## Completed Tasks (last 7 days) with Results

### 2025-12-09
- **API integration task**
  - Result: Configuration completed
  - Note: Requires further setup

### 2025-12-08
- **API review**
  - Result: 3 issues found, tickets created
```

## Typical Vault Areas for Tasks

| Area | Path | Typical Tasks |
|------|------|---------------|
| Daily Notes | `Daily/` | Daily ad-hoc tasks |
| Areas | `Areas/` | Area-specific tasks |
| Projects | `Projects/` | Project tasks |
| Agent | `Agent/` | Agent-specific tasks |

## Edge Cases

### Ignore Empty Tasks

Some files have empty checkboxes (`- [ ]` without text). Filter with:
```
t.text != ""
```

### Tasks vs. Checkboxes in Lists

Dataview only recognizes real tasks (`- [ ]`), not normal bullets. This is correct.

### Large Result Sets

For vault-wide queries, results can be large:
- Use `LIMIT` in the query
- Filter further client-side
- Ask user for constraints (time range, folder, tag)

## Limitations

- **No sorting by task fields:** Dataview sorts by file metadata, not by task.completion
- **Client-side filtering needed:** The WHERE clause filters files, not tasks - all tasks of the file come along
- **No Tasks plugin features:** Recurring tasks, priorities (⏫) are recognized as text, not as fields

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mhenze-exaring) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
