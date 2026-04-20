---
name: persona-learning
description: | Use when this capability is needed.
metadata:
  author: hiivmind
---

# Persona Learning System

This skill implements the learning capture, review, and promotion system for AI Persona OS. It provides structured mechanisms for capturing learnings, errors, and feature requests, then promotes valuable patterns to long-term memory.

## Phase 1: Capture

Log structured entries to the appropriate file in `~/workspace/.learnings/`:

### Step 1.1: Determine Entry Type

Classify the entry:
- **Learning** → `LEARNINGS.md` (insights, discoveries, successful approaches)
- **Error** → `ERRORS.md` (mistakes, failures, incorrect assumptions)
- **Feature Request** → `FEATURE_REQUESTS.md` (desired capabilities, tool gaps, workflow improvements)

### Step 1.2: Ensure Learning Directory Exists

```pseudocode
if not exists(~/workspace/.learnings/):
    create directory ~/workspace/.learnings/

if not exists(target_file):
    create target_file with header
```

### Step 1.3: Format Entry

Structure the entry with these fields:

```markdown
## [YYYY-MM-DD] Category: Brief Title

**Description:** Clear explanation of what was learned/error encountered/feature needed

**Context:** Situation that led to this learning/error/need
- Relevant project/task
- What was attempted
- What the goal was

**Resolution/Action:**
- For learnings: What worked, why it worked, when to apply
- For errors: Root cause, how it was fixed, prevention strategy
- For feature requests: Proposed solution, workaround until implemented

**Tags:** #keyword1 #keyword2 #keyword3

**Status:** Active | Promoted | Resolved
```

### Step 1.4: Append to Appropriate File

```pseudocode
Read target_file
Append formatted_entry to target_file
Write updated target_file
Confirm to user: "Entry captured in {target_file}"
```

## Phase 2: Review

Scan learning files for patterns and promotion candidates.

### Step 2.1: Scan All Learning Files

```pseudocode
learnings = Read ~/workspace/.learnings/LEARNINGS.md
errors = Read ~/workspace/.learnings/ERRORS.md
feature_requests = Read ~/workspace/.learnings/FEATURE_REQUESTS.md (if exists)

entries = parse_all_entries(learnings, errors, feature_requests)
```

### Step 2.2: Detect Patterns

Look for:

1. **Repeated Mistakes** (same error type 3+ times)
2. **Common Themes** (tags appearing in 5+ entries)
3. **Promotion Candidates** (Status: Active, mentioned 3+ times)
4. **Resolved Patterns** (multiple entries with same root cause)

```pseudocode
patterns = {
    "repeated_errors": [],
    "common_tags": {},
    "promotion_candidates": [],
    "resolved_themes": []
}

for entry in entries:
    if entry.status == "Active":
        increment tag_counts[entry.tags]

    if entry appears 3+ times (by description similarity):
        add to promotion_candidates

    if entry.type == "error" and similar_errors >= 3:
        add to repeated_errors
```

### Step 2.3: Generate Review Summary

Output format:

```markdown
# Learning Review - [Date]

## Summary Statistics
- Total learnings: {count}
- Total errors: {count}
- Feature requests: {count}
- Promoted entries: {count}

## Patterns Detected

### Repeated Errors ({count})
1. [Error theme] - {count} occurrences
   - Last seen: [date]
   - Prevention strategy: [if available]

### Common Themes ({count} tags with 5+ mentions)
- #tag1: {count} entries
- #tag2: {count} entries

### Promotion Candidates ({count})
1. [Title] - {count} occurrences
   - First seen: [date]
   - Last seen: [date]
   - Ready for promotion to MEMORY.md

### Resolved Patterns
1. [Theme] - {count} instances resolved
   - Resolution: [summary]
```

**Ask user for review preferences:**

```json
{
  "questions": [
    {
      "id": "review_frequency",
      "question": "Would you like to schedule weekly automated reviews, or run reviews manually?",
      "type": "choice",
      "choices": ["Weekly automated", "Manual only"]
    }
  ]
}
```

## Phase 3: Promote

Elevate repeated patterns (3+ occurrences) to `~/workspace/MEMORY.md`.

### Step 3.1: Identify Promotion Candidates

```pseudocode
candidates = []
for entry in all_entries:
    if entry.status == "Active":
        occurrences = count_similar_entries(entry)
        if occurrences >= 3:
            candidates.append(entry)
```

### Step 3.2: Request User Approval

**Before modifying MEMORY.md, always ask:**

```json
{
  "questions": [
    {
      "id": "promotion_approval",
      "question": "Found {count} entries ready for promotion to MEMORY.md. Review candidates:\n\n{candidate_summaries}\n\nProceed with promotion?",
      "type": "confirm"
    }
  ]
}
```

### Step 3.3: Promote to MEMORY.md

```pseudocode
if user_approves:
    memory = Read ~/workspace/MEMORY.md

    for candidate in approved_candidates:
        promoted_entry = format_promotion(candidate)
        append promoted_entry to memory

        # Mark original as promoted
        update_entry_status(candidate, "Promoted")

    Write memory to ~/workspace/MEMORY.md
    Confirm: "Promoted {count} entries to MEMORY.md"
```

**Promotion format:**

```markdown
## [Pattern Name]

**Source:** {original_file} - {dates of occurrences}

**Summary:** {consolidated description from all occurrences}

**Application:** {when to apply this learning}

**Evidence:** {count} occurrences between {first_date} and {last_date}
```

### Step 3.4: Update Original Entries

```pseudocode
for promoted_entry in promoted_list:
    locate original_entry in source_file
    change status from "Active" to "Promoted"
    add reference: "See MEMORY.md: [Pattern Name]"
```

## Phase 4: Growth Loops

Implement 4 continuous feedback cycles that run alongside normal operations.

### Loop 1: Curiosity Loop

**Purpose:** Systematically fill knowledge gaps.

```pseudocode
# Ongoing behavioral pattern
while working_on_task:
    if encounter_unknown:
        log to computed.knowledge_gaps[]
        ask 1-2 clarifying questions

        if pattern_emerges (3+ similar gaps):
            propose adding to ~/workspace/USER.md
            generate targeted learning ideas
```

**Example state tracking:**

```pseudocode
computed.knowledge_gaps = [
    {topic: "Docker networking", count: 1, first_seen: "2026-02-15"},
    {topic: "Kubernetes ingress", count: 3, first_seen: "2026-02-10"}
]

if knowledge_gaps["Kubernetes ingress"].count >= 3:
    suggest: "Add Kubernetes ingress learning to USER.md goals"
```

### Loop 2: Pattern Recognition Loop

**Purpose:** Automate repeated manual work.

```pseudocode
while handling_request:
    track request_type in computed.request_history[]

    if same_request_type >= 3:
        propose: "This is the 3rd time you've needed {task}. Build automation?"

        if user_approves:
            design system
            build with approval
            document in ~/workspace/WORKFLOWS.md
```

**Example:**

```pseudocode
computed.request_history = [
    {type: "format_json", count: 1},
    {type: "git_commit", count: 5},  # automation candidate
    {type: "run_tests", count: 2}
]

if request_history["git_commit"].count >= 3:
    propose: "Create /commit skill to automate git workflows?"
```

### Loop 3: Capability Expansion Loop

**Purpose:** Overcome limitations through tooling.

```pseudocode
while attempting_task:
    if hit_limitation:
        log to computed.capability_gaps[]
        research available_tools/skills

        if solution_found:
            install/build tool
            document in ~/workspace/TOOLS.md
            retry original problem
```

**Example:**

```pseudocode
if cannot_parse_yaml:
    research: "YAML parsing tools for Claude Code"
    find: yq tool
    install: yq
    document: "Added yq for YAML processing" → TOOLS.md
    apply to original_task
```

### Loop 4: Outcome Tracking Loop

**Purpose:** Learn from decisions through follow-up.

```pseudocode
while making_significant_decision:
    log decision to computed.pending_outcomes[]

    after time_passes (weekly/monthly):
        review outcome
        extract lessons
        update approach based on results
```

**Example:**

```pseudocode
computed.pending_outcomes = [
    {
        decision: "Chose PostgreSQL over MySQL",
        date: "2026-02-01",
        follow_up_date: "2026-03-01",
        outcome: null  # check later
    }
]

# On follow-up date:
review PostgreSQL choice:
    if successful: document why it worked
    if problematic: document what to do differently
    update MEMORY.md with lesson
```

## State Management

Track growth loop state in `computed.*` variables:

```pseudocode
computed.knowledge_gaps = []
computed.request_history = {}
computed.capability_gaps = []
computed.pending_outcomes = []
computed.last_review_date = "YYYY-MM-DD"
computed.promotion_candidates = []
```

## Key Rules

1. **Always ask before modifying MEMORY.md** - promotion requires explicit approval
2. **Structured entries only** - enforce the entry format template
3. **Growth loops are behavioral patterns** - they run continuously, not as one-shot procedures
4. **3x repetition threshold** - patterns need 3+ occurrences before promotion
5. **Source tracking** - promoted entries reference original locations and dates
6. **Status transitions** - Active → Promoted (never delete originals)

## Usage Examples

### Capturing a Learning

```
User: "I just discovered that using --depth=1 speeds up git clones significantly"

Claude: Creates entry in ~/workspace/.learnings/LEARNINGS.md:

## [2026-02-17] Git: Shallow Clone Optimization

**Description:** Using git clone --depth=1 creates a shallow clone with only the latest commit, significantly reducing clone time and disk space for large repositories.

**Context:** Working with large monorepos
- Attempted: Regular git clone on 5GB repository
- Goal: Faster local setup for CI/CD
- Result: Clone time reduced from 15min to 2min

**Resolution/Action:**
- Use --depth=1 for CI builds where history isn't needed
- Works best for: testing, deployment pipelines, one-time inspections
- Avoid for: repositories where you need full git history
- Can convert to full clone later: git fetch --unshallow

**Tags:** #git #performance #optimization #devops

**Status:** Active
```

### Running a Review

```
User: "Run a learning review"

Claude: Scans all .learnings/ files, generates summary:

# Learning Review - 2026-02-17

## Summary Statistics
- Total learnings: 23
- Total errors: 8
- Feature requests: 5
- Promoted entries: 3

## Patterns Detected

### Promotion Candidates (2)
1. Git shallow clone optimization - 3 occurrences
   - First seen: 2026-01-15
   - Last seen: 2026-02-17
   - Ready for promotion to MEMORY.md

2. Docker layer caching - 4 occurrences
   - First seen: 2026-01-20
   - Last seen: 2026-02-15
   - Ready for promotion to MEMORY.md

Would you like to promote these 2 entries to MEMORY.md?
```

### Growth Loop Example

```
# Curiosity Loop
User: "How do I configure Kubernetes ingress?"

Claude tracks:
computed.knowledge_gaps["Kubernetes"] = 3  # third time

Claude: "I notice this is the third time we've encountered Kubernetes questions. Would you like me to add 'Learn Kubernetes networking' to your USER.md learning goals? I can also research relevant documentation to add to your corpus."

# Pattern Recognition Loop
User: "Format this JSON" (5th time)

Claude: "This is the 5th time you've needed JSON formatting. Would you like me to create a /format-json skill that handles this automatically? It could:
- Detect JSON in clipboard/selection
- Auto-format with proper indentation
- Validate syntax
- Handle both compact and pretty-print modes"

# Capability Expansion Loop
Claude hits limitation parsing YAML

Claude: "I need a YAML parser to complete this task. Let me research options... Found: yq tool. Installing and documenting in TOOLS.md..."

# Outcome Tracking Loop
User: "Should we use REST or GraphQL for this API?"

Claude: "Let's go with GraphQL for these reasons: [analysis]. I'll track this decision and follow up in 2 weeks to evaluate if it's working as expected."

computed.pending_outcomes.append({
    decision: "GraphQL API choice",
    rationale: "[reasons]",
    follow_up: "2026-03-03"
})
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hiivmind) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
