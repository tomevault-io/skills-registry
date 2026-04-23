---
name: speclet-ticket
description: description: Convert a large draft into individual tickets for isolated implementation sessions Use when this capability is needed.
metadata:
  author: danielcastro-dev
---
---
name: speclet-ticket
description: Convert a large draft into individual tickets for isolated implementation sessions
license: MIT
compatibility: opencode
metadata:
  workflow: speclet
  phase: planning
---

# Speclet Ticket Skill

Convert a large draft document into individual tickets for isolated implementation sessions.

## What I Do

- Read `.speclet/draft.md` and identify discrete work items
- Create ticket folders at `.speclet/tickets/TICKET-N/`
- Generate ticket metadata at `.speclet/tickets/TICKET-N/ticket.json`
- Copy general draft to `.speclet/tickets/TICKET-N/ticket-draft.md`
- Create/update `.speclet/tickets/index.json` for centralized status tracking
- Delete root `draft.md` **only after ALL tickets are successfully created**
- Enable ticket-by-ticket workflow: one ticket → one session → full speclet cycle

## When to Use Me

Use this when:
- A draft contains multiple independent fixes/features
- The combined scope would overwhelm LLM context
- You want to work on items one at a time across sessions

## Your Task

### Step 1: Read the Draft

```
Read .speclet/draft.md
```

Identify discrete work items. Each item that can be implemented independently = 1 ticket.

**Splitting Rule:** 1 ticket = 1 friction/problem. Maximum atomicity.

### Step 2: Ask Clarifying Questions (if needed)

If the draft is ambiguous about how to split, ask questions with lettered options.

**Example:**
```
1. How should I group these items?
   A. One ticket per file mentioned ⭐ Recommended — maximum isolation
   B. Group by feature area
   C. Group by complexity
   D. Keep as single ticket

   **Reason for recommendation:** Isolated tickets prevent context bleed and allow
   prioritization between sessions.
```

## Global Rules

### Always Show Recommendation + Reason

When asking questions with options, ALWAYS:
1. Mark the recommended option with ⭐
2. Add `**Reason for recommendation:**` explaining why

**Example format:**
```
1. [Question]?
   A. Option A
   B. Option B ⭐ Recommended — [brief reason]
   C. Option C

   **Reason for recommendation:** [Detailed explanation of why B is best]
```

### Step 3: Create Ticket Files and Folders

For each discrete item:

1. **Create ticket folder:** `.speclet/tickets/TICKET-N/`
2. **Copy draft to folder:** `.speclet/tickets/TICKET-N/ticket-draft.md`
3. **Create ticket JSON:** `.speclet/tickets/TICKET-N/ticket.json`

**IMPORTANT:** Complete ALL tickets before proceeding to Step 4. Do NOT delete draft.md until all tickets exist.

#### Ticket JSON Structure

```json
{
  "specletVersion": "1.0",
  "id": "TICKET-1",
  "title": "Short descriptive title",
  "description": "2-3 sentences explaining the problem/friction and desired outcome",
  "files": ["path/to/likely/affected/file.ts"],
  "sourceContext": ".speclet/draft.md", 
  "preliminaryCriteria": [
    "Preliminary acceptance criterion (will be refined in speclet-draft)",
    "Another criterion"
  ],
  "priority": 1,
  "dependsOn": [],
  "retryHints": [],
  "status": "pending"
}
```

#### Field Descriptions

| Field | Required | Description |
|-------|----------|-------------|
| `specletVersion` | Yes | Always `"1.0"` — identifies this as a speclet ticket |
| `id` | Yes | Unique identifier (TICKET-1, TICKET-2, etc.) |
| `title` | Yes | Short title (< 80 chars) |
| `description` | Yes | 2-3 sentences explaining the problem and desired outcome |
| `files` | Yes | Array of likely affected files (best guess, refined later) |
| `sourceContext` | Yes | **Absolute path** to the original source document with optional anchor |
| `preliminaryCriteria` | Yes | Initial acceptance criteria (refined during speclet-draft) |
| `priority` | Yes | Numeric priority (1 = highest) |
| `dependsOn` | No | Array of ticket IDs that must complete first |
| `retryHints` | No | Accumulated hints from failed attempts |
| `status` | Yes | One of: `pending`, `in_progress`, `done`, `blocked` |

#### specletVersion Field (CRITICAL)

The `specletVersion` field is **mandatory**. It serves two purposes:

1. **Identification:** Allows `speclet-draft` to recognize this as a speclet-generated ticket
2. **Compatibility:** Future versions can check compatibility

**If `specletVersion` is missing, other skills will NOT recognize this as a speclet ticket.**

#### sourceContext Best Practices

The `sourceContext` field must point to the **original source** of the requirement:

✅ **Good sourceContext values:**
- `.speclet/draft.md`
- `GitHub Issue #123`
- `Code review comment on PR #456`

❌ **Bad sourceContext values:**
- `TICKET-1.json` — Self-referential
- `"From the analysis"` — Not traceable

### Step 4: Create/Update Index

Create or update `.speclet/tickets/index.json`:

```json
{
  "specletVersion": "1.0",
  "source": "docs/original-analysis.md",
  "createdAt": "2025-01-09T15:30:00Z",
  "tickets": [
    {
      "id": "TICKET-1",
      "title": "Fix empty state guidance",
      "status": "pending",
      "priority": 1
    },
    {
      "id": "TICKET-2", 
      "title": "Improve serial scanner UX",
      "status": "pending",
      "priority": 2
    }
  ]
}
```

**Note:** The `source` field in index.json should point to the **original source document** `.speclet/draft.md`.

### Step 5: Delete Root Draft (ONLY AFTER ALL TICKETS CREATED)

**Pre-condition check (MANDATORY):**

Before deleting, verify:
1. All ticket folders exist: `.speclet/tickets/TICKET-1/`, `.speclet/tickets/TICKET-2/`, etc.
2. Each folder has `ticket.json` and `ticket-draft.md`
3. `index.json` exists and lists all tickets

```bash
# Verification command
ls .speclet/tickets/*/ticket.json | wc -l  # Should equal number of tickets
ls .speclet/tickets/*/ticket-draft.md | wc -l  # Should equal number of tickets
```

**Only if verification passes:**

```bash
rm .speclet/draft.md
```

**If verification fails:** STOP. Do not delete draft.md. Report which tickets are missing.

This ensures:
- No confusion about which draft to use
- Clean state for next workflow
- Each ticket has its own `ticket-draft.md` as context

### Step 6: Confirm with User

After creating tickets, summarize:

```
✅ Created N tickets from draft.md

| ID | Title | Priority | Dependencies |
|----|-------|----------|--------------|
| TICKET-1 | [title] | 1 | - |
| TICKET-2 | [title] | 2 | TICKET-1 |

📁 Structure created:
.speclet/tickets/
├── index.json
├── TICKET-1/
│   ├── ticket.json
│   └── ticket-draft.md
├── TICKET-2/
│   ├── ticket.json
│   └── ticket-draft.md

🗑️ Deleted: .speclet/draft.md

**Next steps:**
1. Start new session
2. Pick a ticket: "Use speclet-draft for TICKET-1"
3. Complete full cycle: draft → spec → loop
4. Come back for next ticket
```

## Workflow Integration

```
PHASE 1: Analysis (this session)
────────────────────────────────
speclet-draft (general analysis)
     │
     ▼
.speclet/draft.md
     │
     ▼
speclet-ticket (this skill)
     │
     ├── Creates .speclet/tickets/TICKET-N/ticket.json
     ├── Creates .speclet/tickets/TICKET-N/ticket-draft.md
     ├── Creates .speclet/tickets/index.json
     ├── Verifies ALL tickets created successfully
     ├── Deletes .speclet/draft.md (only after verification)
     └── END of session

════════════════════════════════════════════════════════════

PHASE 2: Per-ticket work (new session)
──────────────────────────────────────
User: "speclet-draft for TICKET-1"
     │
     ├── Reads .speclet/tickets/TICKET-1/ticket.json (validates specletVersion)
     ├── Reads .speclet/tickets/TICKET-1/ticket-draft.md
     ├── Asks clarifying questions
     └── Creates .speclet/draft.md (refined)
     │
     ▼
speclet-spec → .speclet/spec.json
     │
     ▼
speclet-loop → implements
     │
     ▼
On completion:
├── Move draft.md → tickets/TICKET-1/draft.md
├── Move spec.json → tickets/TICKET-1/spec.json
├── Update ticket.json status to "done"
└── Ready for next ticket
```

## Post-Completion Artifact Preservation

After completing a ticket with `speclet-loop`, preserve artifacts:

```bash
# Move refined draft (keeps ticket-draft.md as original context)
mv .speclet/draft.md .speclet/tickets/TICKET-1/draft.md

# Move spec
mv .speclet/spec.json .speclet/tickets/TICKET-1/spec.json

# Update ticket status
# Edit .speclet/tickets/TICKET-1/ticket.json: "status": "done"
```

Final structure for completed ticket:
```
.speclet/tickets/TICKET-1/
├── ticket.json        ← Metadata (status: done)
├── ticket-draft.md    ← Original context (from speclet-ticket)
├── draft.md           ← Refined draft (from speclet-draft for TICKET-1)
└── spec.json          ← Implementation spec (from speclet-spec)
```

## Rules

- **One friction = one ticket** — Maximum atomicity
- **specletVersion required** — All tickets must have `"specletVersion": "1.0"`
- **ticket-draft.md in each folder** — Copy of general draft for context
- **Delete root draft after tickets** — Clean state for next workflow
- **Dependencies are declarative** — Field exists but no automated blocking
- **Preliminary criteria only** — Real criteria come from speclet-draft
- **Status tracking** — Update index.json when ticket status changes
- **sourceContext = original source** — Point to real origin, not speclet artifacts

## Output

- Ticket folders: `.speclet/tickets/TICKET-N/`
- Ticket metadata: `.speclet/tickets/TICKET-N/ticket.json`
- Ticket context: `.speclet/tickets/TICKET-N/ticket-draft.md`
- Central index: `.speclet/tickets/index.json`
- Root draft: **DELETED** (only after all tickets verified)

When complete:

> "Tickets created in `.speclet/tickets/`. Root draft deleted. Start new session and use: speclet-draft for TICKET-1"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danielcastro-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
