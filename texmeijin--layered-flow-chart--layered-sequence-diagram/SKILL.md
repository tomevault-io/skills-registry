---
name: layered-sequence-diagram
description: | Use when this capability is needed.
metadata:
  author: texmeijin
---

# Layered Sequence Diagram

Create, update, and refine interactive hierarchical sequence diagrams from a single HTML file. Interaction blocks are clickable — drilling down opens stacking modal layers with progressively finer detail.

## Mode Selection

Before starting, determine which mode applies:

| Mode | Trigger | Existing data.js? | Code investigation? |
|------|---------|-------------------|-------------------|
| **Create** | "Make a sequence diagram of X" | No | Full (3-phase pipeline) |
| **Update** | "Update the sequence to reflect latest code" | Yes | Targeted (diff-focused) |
| **Refine** | "Improve the layout / add details / add fragments" | Yes | None or minimal |

**How to determine the mode:**
1. Check if user references an existing sequence diagram (directory with `index.html` + `data.js`) → if yes, **Update** or **Refine**
2. If user mentions code changes, new features, or syncing → **Update**
3. If user mentions layout, visuals, detail level, or adding properties like `boundary` → **Refine**
4. Otherwise → **Create**

---

## Create Mode (3-Phase SubAgent Pipeline)

This mode requires deep code investigation BEFORE building the HTML. To prevent shallow analysis, **you MUST split the work into 3 phases using SubAgents**. Do NOT skip phases or combine them.

### Phase 1: Investigation (Serial Explore SubAgents)

Investigation is done in **serial steps** using multiple Explore agents. Each step builds on the previous output. Do NOT parallelize — the layered nature requires understanding the high level before deciding what to drill into.

#### Step 1-1: Broad Survey (Explore)

Identify the **participants** (actors/systems/services) and the **high-level message flow** between them. This becomes the **root level** of the diagram.

**Prompt template:**
```
Investigate the following interaction/communication flow in this codebase: {user's description}

Produce a HIGH-LEVEL sequence overview. Focus on WHICH actors communicate and WHAT messages they exchange, NOT implementation details.

## Flow Overview
- Purpose: (one sentence)
- Entry point: (file:line)
- Overall pattern: (e.g., request-response, event-driven, saga, pipeline)

## Participants (aim for 3-6):
For each actor/system/service:
1. **{Name}**
   - Role: {what this participant does, one sentence}
   - Type: client | server | database | external-service | queue | worker
   - Key file(s): {primary file(s)}

## Message Sequence (in order):
For each message in the flow:
1. {Sender} → {Receiver}: **{message label}** ({sync|async|reply})
   - Purpose: {what this message does}
   - Complexity: simple | moderate | complex

## Interaction Blocks:
Group related messages into logical blocks:
- **{Block Name}** (messages {N}-{M}): {brief description}
  - Complexity: simple | complex (complex = warrants drill-down)

Focus on identifying WHO talks to WHOM and in WHAT order. Do not drill into implementation details yet.
```

#### Main Agent Review (between steps)

After Step 1-1, review the output and decide:
- Which interaction blocks are "complex" enough to warrant a drill-down level?
- Are there participants that were missed?
- Select **2-4 interaction blocks** for deep investigation in Step 1-2.

#### Step 1-2: Deep Dive (Explore)

Investigate the selected blocks in detail. These become the **drill-down levels**.

**Prompt template:**
```
I am investigating this communication flow: {user's description}

Here is the high-level sequence already produced:
{paste Step 1-1 output}

Now deep-dive into these specific interaction blocks: {list of selected blocks}

For EACH block, produce a detailed sub-sequence:

### Block: {name}
#### Additional Participants (if any):
- Internal services, validators, helpers that participate only at this detail level

#### Detailed Message Sequence:
1. {Sender} → {Receiver}: **{message label}** ({sync|async|reply|self})
   - File: {file:line-range}
   - Tech: {libraries/protocols}
   - Input: {what is sent}
   - Output: {what is returned}
   - Details: {error handling, edge cases, important notes}
   - Note: {annotation text if helpful}

#### Fragments:
- {type: loop|alt|opt|break} "{label}" [{condition}] wrapping messages {N}-{M}

Investigate EVERY file involved. List concrete function names, file paths, and line numbers. Aim for 6-12 messages per block.
```

#### Step 1-3 (Optional): Revise Root Level

If the deep dive revealed corrections needed at the root level, spawn one more **Explore** agent.

### Phase 2: Data Construction (SubAgent: general-purpose)

Spawn a **general-purpose** agent. Pass it the Phase 1 report AND the Data Schema section below.

**Prompt template:**
```
Convert the following investigation report into a LEVELS JavaScript object for a sequence diagram.

## Investigation Report
{paste Step 1-1 output (with Step 1-3 corrections applied if any)}

## Deep Dive Report
{paste Step 1-2 output}

## Schema Rules
{paste the "Data Schema" section from this SKILL.md}

Output ONLY the JavaScript for data.js:
- const LEVELS = { ... };
- const HEADER_LOGO = '...';
- document.title = '... | Layered Sequence Diagram';

Requirements:
- Root level: 3-6 participants, all high-level messages, fragments wrapping drillable blocks
- Each drill-down level may introduce additional participants not visible at root (e.g., internal validators, queues)
- Set hasChildren: true on fragments that have matching LEVELS entries
- Include file references, techs, details where available from the investigation
- Order messages strictly in the sequence they occur
- Use boundary: true for cross-network/cross-process messages
- Use fragment types appropriately: ref for drill-down, loop/alt/opt/break for control flow
```

### Phase 2.5: Ask Output Location (Main Agent — AskUserQuestion)

Before assembling the HTML, **use AskUserQuestion** to ask the user where to place the generated file.

**AskUserQuestion config:**
- header: "Output path"
- question: "シーケンス図の出力先ディレクトリを選んでください。"
- options:
  1. **`/tmp` 配下 (Recommended)** — `/tmp/{project}/seq-{name}/` に出力します。プロジェクトを汚しません。
  2. **メインファイルと同じフォルダ** — `{detected main file's directory}/seq-{name}/` に出力します。
  3. **パスを指定する** — 任意のディレクトリパスを入力できます。

### Phase 3: Assembly (Main Agent)

1. **Create output directory**: `mkdir -p {output-dir}`
2. **Copy template**: `cp ~/.claude/skills/layered-sequence-diagram/assets/template.html {output-dir}/index.html`
3. **Write data.js**: Write Phase 2 output to `{output-dir}/data.js`
4. **Open in browser**: `open {output-dir}/index.html`
5. **Verify** with screenshots — click through every drill-down level

---

## Update Mode (Sync with Code Changes)

### Step 0: Extract Current State

1. **Read the existing `data.js` file** to extract the current `LEVELS` object
2. Parse it to understand: which participants, messages, fragments, and hierarchy exist

### Step 1: Targeted Investigation (Explore)

Spawn an **Explore** agent focused on **what changed**.

**Prompt template:**
```
An existing sequence diagram describes this flow: {brief description}

Here are the current participants, messages, and fragments:
{paste extracted LEVELS summary}

The user says the following has changed: {user's description}

Investigate the codebase and report:
1. **New messages** that should be added (with file:line, participants, type)
2. **Removed messages** that no longer exist in the code
3. **Modified messages** where the label, participants, or behavior changed
4. **New/removed participants**
5. **Fragment changes** (new, removed, or adjusted ranges)

Only report actual changes - do not re-describe unchanged parts.
```

### Step 2: Apply Changes

Based on the diff report, update the LEVELS object:
- Add/remove participants and messages
- Adjust fragment `fromMsg`/`toMsg` indices when messages are added/removed
- Update drill-down levels if affected

### Step 3: Write and Verify

1. **Edit the existing `data.js` file** in-place
2. **Open in browser** and verify all layers

---

## Refine Mode (Improve Existing Diagram)

Use when the diagram's content is correct but the presentation needs improvement.

### Common Refinements

| Request | Action |
|---------|--------|
| "Add boundary arrows" | Add `boundary: true` to cross-network messages |
| "Add more detail to X" | Enrich messages with details/techs/file fields |
| "Add a drill-down for X" | New level + set `hasChildren` on fragment |
| "Add fragments" | Wrap related messages with loop/alt/opt fragments |
| "Add notes" | Add `note` fields to explain timing or context |

### Steps

1. **Read the existing `data.js` file**
2. **Apply changes** directly to the LEVELS object
3. **Edit `data.js` in-place** — do not touch `index.html`
4. **Open and verify**

---

## Data Schema

The `LEVELS` object is a flat map. Key = level ID, value = level definition.

```js
const LEVELS = {
  root: { ... },          // Required. Entry point.
  'fragment-id': { ... }, // Sub-level for a fragment with hasChildren: true
};
```

### Level Definition

```js
{
  title: 'Level Title',
  description: 'Subtitle text',
  participants: [ /* Participant[] */ ],
  messages: [ /* Message[] */ ],
  fragments: [ /* Fragment[] — optional */ ],
}
```

### Participant

```js
{
  id: 'unique-id',           // Used in message from/to
  name: 'Display Name',
  icon: 'emoji',             // Single emoji
  color: '#6366f1',          // Border color (hex)
}
```

### Message

```js
{
  id: 'unique-id',
  from: 'participant-id',    // Sender
  to: 'participant-id',      // Receiver (same as from for self-message)
  label: 'Message text',     // Arrow label
  type: 'sync',              // sync | async | reply | self
  // Optional fields:
  boundary: true,            // Cross-network/process boundary (indigo style)
  note: 'Annotation text',   // Shown above the arrow in italics
  techs: ['Stripe', 'gRPC'], // Tech badges in popover
  file: 'src/foo.ts:10-20',  // Source file reference in popover
  input: 'Request body',     // Input description in popover
  output: 'JSON response',   // Output description in popover
  details: ['point 1', ...], // Bullet list in popover
}
```

### Message Types

| Type | Arrow Style | Use Case |
|------|------------|----------|
| `sync` | Solid line, filled arrowhead | Synchronous call (HTTP request, function call) |
| `async` | Solid line, filled arrowhead | Asynchronous message (webhook, queue publish) |
| `reply` | Dashed line, open arrowhead | Response to a previous sync call |
| `self` | Loop on same lifeline | Internal processing (parse, validate, transform) |

### Fragment

```js
{
  id: 'unique-id',
  type: 'ref',              // ref | loop | alt | opt | break
  label: 'Display Name',
  condition: 'guard text',  // Optional — shown as [condition]
  fromMsg: 0,               // Start message index (inclusive)
  toMsg: 3,                 // End message index (inclusive)
  hasChildren: true,         // true = clicking opens drill-down (needs matching LEVELS entry)
  color: '#custom',          // Optional accent color override
}
```

### Fragment Types

| Type | Purpose | Typical `hasChildren` |
|------|---------|----------------------|
| `ref` | Reference to detailed sub-sequence | `true` (drill-down) |
| `loop` | Repeated execution | `false` |
| `alt` | Conditional branch (if/else) | `false` |
| `opt` | Optional execution (if) | `false` |
| `break` | Exit from enclosing fragment | `false` |

### Boundary Messages

Use `boundary: true` for messages that **cross a network or process boundary**. These render with indigo styling.

**When to use:**
- Client → Server (browser → API)
- Server → External API (Stripe, SendGrid)
- Server → Database (TCP connection)
- Service → Message Queue

**When NOT to use:**
- Function calls within the same process
- Internal module references

## Participant Design Guidelines

- Root level: 3-6 participants (actors visible at the highest abstraction)
- Drill-down levels: May introduce **additional participants** not in the root — this is a key value of hierarchical sequence diagrams. Internal validators, caches, queues, and helpers that clutter the root view belong in drill-downs.
- Keep participant order consistent: left = initiator, right = responder/storage

## Message Ordering

Messages are rendered top-to-bottom in array order. The order must reflect the actual temporal sequence of the interaction.

## Color Palette Suggestions

| Participant Type | Hex |
|-----------------|-----|
| Client/User | `#3B82F6` |
| Frontend/UI | `#8B5CF6` |
| API Server | `#6366f1` |
| External Service | `#EF4444` |
| Database/Storage | `#10B981` |
| Queue/Worker | `#F59E0B` |
| Analytics/Monitoring | `#14B8A6` |

## Key Implementation Notes

- **Do NOT modify `index.html`** (the template) — only generate/edit `data.js`
- Each drill-down level has its own `participants` array — participants can differ between levels
- Fragment `fromMsg`/`toMsg` are zero-based indices into the level's `messages` array
- When adding/removing messages in Update mode, recalculate all fragment indices
- Depth 3+ works but modals become narrow — 2-3 levels is the sweet spot

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/texmeijin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
