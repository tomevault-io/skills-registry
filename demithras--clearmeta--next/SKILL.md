---
name: next
description: Show ALL available leaf tasks with feature paths, pick by number Use when this capability is needed.
metadata:
  author: demithras
---

# /next — Show All Available Work, Pick by Number

> **Purpose**: Display ALL leaf-level tasks (unblocked, lowest-level) with feature paths, sorted by score. User picks by number.

The prompter (human or agent) sees **everything available**, sorted by priority/type/recency. Top item = recommended. Type a number to select.

---

## Syntax

```
/next                    # Show all leaves + recommendation
/next --mine             # Filter to my assignments
/next --unassigned       # Filter to unassigned only
```

---

## What is a "Leaf"?

**Leaf** = lowest-level unblocked task. You can't "do" an epic — only its constituent tasks.

```
Epic (blocked by children)
├── Feature (blocked by children)
│   ├── Task A ← LEAF (no children, unblocked)
│   └── Task B ← LEAF (no children, unblocked)
└── Standalone Task ← LEAF (no children, unblocked)
```

If a task has children that are ready, it's NOT a leaf — its children are.

---

## Agent Execution Guide

### Step 1: Get Ready Issues
```bash
bd ready
```
If empty → output "nothing ready" state and exit.

### Step 2: Leaf-Filter ⚡ (PARALLEL)

```
FOR EACH issue in ready_list (IN PARALLEL — one message, multiple Bash calls):
    bd show [issue-id]

FOR EACH issue:
    IF issue.BLOCKS contains ANY other ready_list issue:
        EXCLUDE (it's a parent, not a leaf)
    ELSE:
        KEEP (it's a leaf)
```

**⚡ CRITICAL**: Run ALL `bd show` calls in ONE message. Sequential = slow.

### Step 3: Build Feature Paths

Using data from Step 2 (`bd show` output), build **feature paths** for each leaf:

**Types in Beads**: `feature`, `task`, `bug`

**Two directions**:
- **↑ Upstream** (DEPENDS ON chain): Trace what this issue depended on, stop at first `feature`
- **↓ Downstream** (BLOCKS chain): Trace what's waiting for this issue, stop at first `feature`

**Algorithm**:
```
FOR EACH leaf:
  upstream_path = []
  TRAVERSE DEPENDS_ON chain until feature found or chain ends:
    IF node.type == "feature":
      upstream_path.unshift(node)
      BREAK
    ELSE:
      upstream_path.unshift(node)

  downstream_paths = []
  FOR EACH branch in BLOCKS:
    path = []
    TRAVERSE until feature found:
      IF node.type == "feature":
        path.push(node)
        downstream_paths.append(path)
        BREAK
      ELSE:
        path.push(node)
```

**Format**: `✨ P1 Feature Name > 📋 Task Name > 📋 Task Name`

### Step 4: Score All Leaves

| Factor | Weight | Values |
|--------|--------|--------|
| Priority | 40% | P0=100, P1=80, P2=60, P3=40, P4=20 |
| Assignment | 30% | Mine=100, Unassigned=70, Others=30 |
| Type | 20% | Bug=90, Task=70, Feature=60 |
| Recency | 10% | Today=100, This week=70, Older=50 |

```
score = (priority × 0.4) + (assignment × 0.3) + (type × 0.2) + (recency × 0.1)
```

Sort by score descending. Top = recommendation.

### Step 5: Output

**Format (UX — human-readable list)**:

```
1. 🐛 P2 **Fix authentication race condition**
   │ ↑ ✨ P1 Feature W > 📋 Task Z
   │ ↓ ✨ P1 Feature X > 📋 Task B
   │ ↓ ✨ P2 Feature Y > 📋 Task B

2. 📋 P2 **Check the Confluence MCP**

3. 📋 P2 **Add test: Max Reportable Demos limit**

4. 📋 P3 **Implement tool auto-detection**
   │ ↓ ✨ P1 /work Umbrella
   │ ↓ ✨ P1 Meta Plugin v1.0

5. 📋 P3 **Audit skills for missing triggers**

6. 📋 P3 **Add behavioral tests**

7. ✨ P3 **Add internal planning tools**

Pick a number:
```

**Key UX points**:
- **Numbered selection** — user types a number to pick
- **Line 1**: `Number` + `Emoji` + `Priority` + **`Bold Title`**
- **Line 2+**: Feature paths (only when they exist)
  - `↑` = upstream (what this came from — DEPENDS ON chain)
  - `↓` = downstream (what this enables — BLOCKS chain)
- **No tiers** — sorted by score, top = recommended
- **No visible scores** — internal only

**Type emoji mapping**:
| Type | Emoji |
|------|-------|
| bug | 🐛 |
| task | 📋 |
| feature | ✨ |

**Nothing ready**:
```
📭 No available work.

Options: `bd blocked` | `bd create --title="..."`
```

### Step 6: State Block (AX — agent-parseable)

```
<!-- next:state
leaves: [
  {
    num: 1,
    id: "meta-abc",
    title: "Fix authentication race condition",
    priority: "P2",
    type: "bug",
    upstream: [{id: "meta-xyz", title: "Feature W", type: "feature", priority: "P1"}],
    downstream: [
      {path: [{id: "meta-def", title: "Task B", type: "task"}, {id: "meta-ghi", title: "Feature X", type: "feature", priority: "P1"}]},
      {path: [{id: "meta-def", title: "Task B", type: "task"}, {id: "meta-jkl", title: "Feature Y", type: "feature", priority: "P2"}]}
    ],
    command: "bd update meta-abc --status=in_progress"
  },
  {
    num: 2,
    id: "meta-mno",
    title: "Check the Confluence MCP",
    priority: "P2",
    type: "task",
    upstream: null,
    downstream: null,
    command: "bd update meta-mno --status=in_progress"
  }
]
filters: {mine: false, unassigned: false}
/next:state -->
```

**AX layer provides**:
- `leaves[]` — all items with full metadata
- `num` — display number for user selection
- `upstream` — path to feature via DEPENDS ON (array of nodes, or null)
- `downstream` — paths to features via BLOCKS (array of path arrays, or null)
- `command` for each leaf — agent can execute directly

---

## Output Format Details

### UX Layer (Human)

**Line 1 — Issue header**:
```
1. 🐛 P2 **Fix authentication race condition**
│  │  │   └── Title (BOLDED — the "what to do")
│  │  └── Priority (P0-P4)
│  └── Type emoji (🐛 bug, 📋 task, ✨ feature)
└── Number (for selection)
```

**Line 2+ — Feature paths** (only if connections exist):
```
   │ ↑ ✨ P1 Feature W > 📋 Task Z
   │ │  │  │  │           └── Intermediate task(s) in path
   │ │  │  │  └── Feature name (terminus)
   │ │  │  └── Feature priority
   │ │  └── Type emoji
   │ └── Direction (↑ upstream / ↓ downstream)
   └── Pipe character (ensures indent visibility)
```

### Feature Paths

- **↑ Upstream**: Trace DEPENDS ON chain to first `feature` type
- **↓ Downstream**: Trace BLOCKS chain to first `feature` type (may have multiple)
- **No paths**: Issue is standalone (no feature connections)

### User Interaction

- **Selection**: User types a number (e.g., `1`, `3`)
- **Top = recommended**: Sorted by score, first item is best pick
- **No commands shown** — clean UX
- **Prompt**: Ends with `Pick a number:`

### AX Layer (Agent)

State block provides everything an agent needs:
- `leaves[].num` — matches display number
- `leaves[].command` — ready-to-execute command
- `leaves[].upstream/downstream` — full path data for context

---

## /next vs /meta

| | /next | /meta |
|---|-------|-------|
| **Shows** | ALL available tasks | Analysis of approaches |
| **Decides** | Recommends one task | Explores options |
| **User role** | Sees all, picks one | Evaluates trade-offs |

**Rule**: `/next` = "here's what's available, I recommend X". `/meta` = "let's think about how to approach this".

---

## Error Handling

| Condition | Output |
|-----------|--------|
| No ready work | "📭 No available work" + options |
| `bd ready` fails | "⚠️ Could not fetch: [error]" |
| All filtered out | "📭 No matching work (X ready, 0 match filters)" |

---

## CLEAR Check (Required Exit)

Every `/next` invocation ends with a CLEAR status after the list:

```
Pick a number:

📋 CLEAR: C✓ L✓ E✓ A✓ R○ | {N} leaves shown | Next: pick a number
```

**Nothing ready**:
```
📭 No available work.

📋 CLEAR: C✓ L✓ E○ A○ R○ | 0 ready | Next: bd create or bd blocked
```

### Structured State (AX)

The `<!-- next:state -->` block (see Step 6) provides agent-parseable data.

---

*Version 3.5.0*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/demithras) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
