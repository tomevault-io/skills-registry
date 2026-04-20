---
name: persona-heartbeat
description: | Use when this capability is needed.
metadata:
  author: hiivmind
---

# Persona Heartbeat

Ambient monitoring and health pulse system for AI Persona OS. This skill handles procedural context health checks, proactive suggestion surfacing, session resumption, and memory maintenance.

## Phase 1: Context Health Check

**Purpose:** Monitor context window usage and trigger appropriate actions based on thresholds.

### Step 1.1: Determine Current Context Usage

Check the current context window usage percentage through available system metrics.

```pseudocode
context_usage_pct = get_context_window_usage()
computed.context_level = context_usage_pct
```

### Step 1.2: Apply Threshold Logic and Surface User-Facing Messages

Based on context usage, determine what the user sees and what actions to take:

| Usage Range | User-Facing Behavior |
|-------------|---------------------|
| < 50% | Nothing — healthy operation |
| 50-69% | Nothing — note internally for tracking |
| 70-84% | "📝 Context at [X]% — saving checkpoint before continuing." then delegate to persona-checkpoint |
| 85-94% | "🟠 Context at [X]% — emergency checkpoint saved. Consider starting a new session soon." |
| 95%+ | "🔴 Context at [X]% — critical. Saving essentials. Please start a new session." |

```pseudocode
if context_usage_pct >= 95:
    show_critical_warning()
    save_essential_state()
    computed.action_taken = "critical_checkpoint"
elif context_usage_pct >= 85:
    show_emergency_warning()
    delegate_to_checkpoint()
    computed.action_taken = "emergency_checkpoint"
elif context_usage_pct >= 70:
    show_checkpoint_notice()
    delegate_to_checkpoint()
    computed.action_taken = "standard_checkpoint"
else:
    computed.action_taken = "none"
```

### Step 1.3: Record Threshold Crossing Events

Track when thresholds are crossed to avoid repeat notifications within the same session.

```pseudocode
if computed.action_taken != "none":
    timestamp = current_timestamp()
    append_to_session_log(threshold_event, timestamp)
```

## Phase 2: Proactive Suggestion Engine

**Purpose:** Surface helpful suggestions when advisor mode is enabled, following strict rules to avoid noise.

**Activation Condition:** Only when advisor mode is ON in USER.md.

### Step 2.1: Check Advisor Mode Status

```pseudocode
user_config = read_file("~/workspace/USER.md")
advisor_enabled = parse_advisor_mode(user_config)
computed.advisor_active = advisor_enabled
```

### Step 2.2: Evaluate Suggestion Criteria

Only surface suggestions when ALL conditions are met:
- Significant new context about user goals discovered
- Spotted unnoticed pattern or opportunity
- Time-sensitive opportunity present
- No complex task currently in progress
- Max 1 suggestion per session not exceeded
- Previous suggestion not ignored/rejected

```pseudocode
if not advisor_enabled:
    return  # Skip suggestion engine entirely

suggestion_contexts = [
    "new_goal_context",
    "unnoticed_pattern",
    "time_sensitive_opportunity"
]

blockers = [
    "complex_task_active",
    "session_quota_exceeded",
    "previous_ignored"
]

if any_suggestion_context() and not any_blocker():
    computed.suggestion_eligible = true
else:
    computed.suggestion_eligible = false
```

### Step 2.3: Format and Surface Suggestion

**Format:**
```
💡 SUGGESTION

[One sentence describing what was noticed]

[One sentence proposing action]

Want me to do this? (yes/no)
```

Example:

<invoke name="$ASK_USER_QUESTION">
<parameter name="question_data">{
  "questions": [
    {
      "question": "💡 SUGGESTION\n\nI noticed you've created three similar scripts in the last hour with overlapping functionality.\n\nWould you like me to consolidate them into a reusable module?\n\nWant me to do this?",
      "key": "suggestion_response",
      "options": ["yes", "no"]
    }
  ]
}</parameter>
</invoke>

```pseudocode
if computed.suggestion_eligible:
    formatted_suggestion = format_suggestion(context, proposal)
    response = ask_user(formatted_suggestion)
    
    if response == "yes":
        execute_suggested_action()
        computed.suggestion_accepted = true
    else:
        log_suggestion_declined()
        computed.suggestion_accepted = false
    
    mark_session_suggestion_quota_used()
```

## Phase 3: Session Start Detection

**Purpose:** Detect new sessions and resume previous work context silently.

### Step 3.1: Detect First Message in New Session

```pseudocode
is_session_start = detect_new_session()
computed.is_new_session = is_session_start

if not is_session_start:
    return  # Skip session start procedures
```

### Step 3.2: Load Core Persona Files Silently

Read foundational files without surfacing content to user.

```pseudocode
soul_content = read_file("~/workspace/SOUL.md")
user_content = read_file("~/workspace/USER.md")
memory_content = read_file("~/workspace/MEMORY.md")

computed.persona_loaded = true
```

### Step 3.3: Check for Yesterday's Log

```pseudocode
yesterday_date = get_yesterday_date()  # Format: YYYY-MM-DD
log_path = "~/workspace/memory/daily-{yesterday_date}.md"

if file_exists(log_path):
    yesterday_log = read_file(log_path)
    computed.has_previous_log = true
else:
    computed.has_previous_log = false
```

### Step 3.4: Surface Uncompleted Items or Stay Silent

```pseudocode
if computed.has_previous_log:
    uncompleted_items = parse_uncompleted_items(yesterday_log)
    
    if uncompleted_items:
        surface_resumption_message(uncompleted_items)
        # Example: "📋 Resuming from last session: 
        # • Fix authentication bug in login flow
        # • Review PR #42 for dependency updates"
    else:
        # Nothing to surface — silent operation
        pass
else:
    # No previous log — silent operation
    pass
```

## Phase 4: Memory Maintenance

**Purpose:** Perform silent housekeeping on memory files and logs, notifying only when action is taken.

**Trigger:** Every ~10 exchanges (approximate, not strict).

### Step 4.1: Check MEMORY.md Size

```pseudocode
memory_file = "~/workspace/MEMORY.md"
file_size = get_file_size(memory_file)

computed.memory_size_kb = file_size / 1024

if computed.memory_size_kb > 4:
    computed.memory_needs_pruning = true
else:
    computed.memory_needs_pruning = false
```

### Step 4.2: Prune Old Memory Entries

Remove entries older than 30 days if file exceeds 4KB.

```pseudocode
if computed.memory_needs_pruning:
    cutoff_date = current_date() - 30_days
    memory_entries = parse_memory_entries(memory_file)
    
    entries_to_keep = filter(lambda e: e.date >= cutoff_date, memory_entries)
    pruned_count = len(memory_entries) - len(entries_to_keep)
    
    if pruned_count > 0:
        write_file(memory_file, entries_to_keep)
        computed.pruned_entries = pruned_count
    else:
        computed.pruned_entries = 0
```

### Step 4.3: Archive Old Daily Logs

Move logs older than 90 days to archive directory.

```pseudocode
log_directory = "~/workspace/memory/"
archive_directory = "~/workspace/memory/archive/"

daily_logs = glob(log_directory + "daily-*.md")
cutoff_date = current_date() - 90_days

logs_to_archive = []
for log in daily_logs:
    log_date = parse_date_from_filename(log)
    if log_date < cutoff_date:
        logs_to_archive.append(log)

if logs_to_archive:
    ensure_directory_exists(archive_directory)
    for log in logs_to_archive:
        move_file(log, archive_directory)
    
    computed.archived_logs = len(logs_to_archive)
else:
    computed.archived_logs = 0
```

### Step 4.4: Check for Uncompleted Items from Previous Days

```pseudocode
recent_logs = get_logs_from_last_7_days("~/workspace/memory/")
uncompleted_items = []

for log in recent_logs:
    items = parse_uncompleted_items(log)
    uncompleted_items.extend(items)

if uncompleted_items and not already_surfaced_this_session():
    surface_once_per_session(uncompleted_items)
    computed.surfaced_uncompleted = true
else:
    computed.surfaced_uncompleted = false
```

### Step 4.5: Notify Only When Action Taken

```pseudocode
actions_taken = []

if computed.pruned_entries > 0:
    actions_taken.append(f"Pruned {computed.pruned_entries} old memory entries")

if computed.archived_logs > 0:
    actions_taken.append(f"Archived {computed.archived_logs} old daily logs")

if actions_taken:
    notification = "🗂️ Housekeeping: " + ", ".join(actions_taken) + "."
    display(notification)
else:
    # Silent operation — no notification
    pass
```

## Phase 5: Heartbeat Output Format

**Purpose:** Standardized format for surfacing health status to user.

### Step 5.1: Determine When to Surface Heartbeat

Surface heartbeat status when:
- Explicitly requested by user ("heartbeat", "status", "health check")
- Context threshold crossed (70%+)
- Housekeeping actions taken
- Uncompleted items found at session start

### Step 5.2: Format Heartbeat Header

```pseudocode
current_datetime = get_current_datetime()  # Format: 2026-02-17 14:30
model_name = get_current_model()  # e.g., "claude-opus-4-6"
version = "1.0.0"  # AI Persona OS version

header = f"🫀 {current_datetime} | {model_name} | AI Persona OS v{version}"
```

### Step 5.3: Generate Traffic Light Indicators

Determine health indicators with blank lines between each:

```pseudocode
indicators = []

# Context health
if computed.context_level < 70:
    indicators.append("🟢 Context: {computed.context_level}% (healthy)")
elif computed.context_level < 85:
    indicators.append("🟡 Context: {computed.context_level}% (attention recommended)")
else:
    indicators.append("🔴 Context: {computed.context_level}% (action required)")

# Memory health
if computed.memory_size_kb < 4:
    indicators.append("🟢 Memory: {computed.memory_size_kb}KB (healthy)")
elif computed.memory_size_kb < 8:
    indicators.append("🟡 Memory: {computed.memory_size_kb}KB (attention recommended)")
else:
    indicators.append("🔴 Memory: {computed.memory_size_kb}KB (action required)")

# Advisor status
if computed.advisor_active:
    indicators.append("🟢 Advisor: Active")
else:
    indicators.append("⚪ Advisor: Inactive")

# Uncompleted items
if computed.surfaced_uncompleted:
    indicators.append("🟡 Uncompleted: Items pending from previous sessions")
```

### Step 5.4: Assemble and Display Heartbeat

```pseudocode
heartbeat_output = header + "\n\n" + "\n\n".join(indicators)
display(heartbeat_output)
```

**Example Output:**

```
🫀 2026-02-17 14:30 | claude-opus-4-6 | AI Persona OS v1.0.0

🟢 Context: 45% (healthy)

🟢 Memory: 2.8KB (healthy)

🟢 Advisor: Active

⚪ Uncompleted: None
```

## Notes

- This skill operates primarily in the background, with most phases running silently
- Only surface information when it provides value to the user
- Respect the max 1 suggestion per session rule strictly
- Memory maintenance should not interrupt active work
- Heartbeat format provides quick visual health assessment

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hiivmind) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
