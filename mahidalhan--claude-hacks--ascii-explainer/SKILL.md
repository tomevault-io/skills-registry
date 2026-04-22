---
name: ascii-explainer
description: Explains code, algorithms, system design using ASCII diagrams. Trigger phrases - "explain visually", "I don't get it", "show me", "ascii diagram", "help me understand". Produces diagram-first explanations ending with TL;DR tables. Use when this capability is needed.
metadata:
  author: mahidalhan
---

## Thinking

Before drawing, DIAGNOSE:
1. **Mental Model Gap**: What does user THINK vs what IS true? Name both.
2. **Primitive**: Which structure captures the essence? (see Quick Pick below)
3. **Hierarchy**: ONE main thing + 2-3 supporting details. Max 6 boxes.
4. **Verify**: Can diagram be understood WITHOUT surrounding text?

**CRITICAL**: Diagnosis IS the skill. Surfacing "user assumes X, but actually Y" is the value—not drawing boxes.

---

## Quick Pick (90% of cases)

| If the concept has... | Use | ASCII Pattern |
|-----------------------|-----|---------------|
| Steps in order | **DAG** | `A → B → C` |
| States + events | **State Machine** | `[S1] --evt--> [S2]` |
| Parent-child hierarchy | **Tree** | `Root` with `/` `\` branches |
| Cycles/feedback | **Graph** | Arrows that loop back |
| Stages that transform | **Pipeline** | `[Stage1] ──▶ [Stage2]` |
| 2D relationships | **Matrix** | Grid with `┌─┬─┐` |

For full primitive taxonomy → see `PRIMITIVES.md`

---

## Primitive Selection Flowchart

```
Continuous (infinite states)? ──Yes──▶ MANIFOLD
         │No
Time/order focused? ──Yes──▶ SEQUENCE│QUEUE│STACK│PIPELINE│TIMELINE
         │No
Concurrent states? ──Yes──▶ PETRI NET
         │No
States + transitions? ──Yes──▶ STATE MACHINE
         │No
Two distinct node types? ──Yes──▶ BIPARTITE
         │No
Edges connect 3+ nodes? ──Yes──▶ HYPERGRAPH
         │No
Partial ordering? ──Yes──▶ LATTICE
         │No
Spatial cells? ──Yes──▶ GRID
         │No
N-dimensional? ──Yes──▶ TENSOR/MATRIX
         │No
Direction matters? ──No──▶ UNDIRECTED GRAPH
         │Yes
Can loop back? ──Yes──▶ CYCLIC GRAPH
         │No
Multiple parents? ──Yes──▶ DAG
         │No──▶ TREE
```

---

## Process

### 1. Clarify (1 question max)
- What are the "things"? (nodes/states/events)
- What are the "connections"? (edges/transitions/order)
- Can you loop back?

If obvious, skip.

### 2. Identify Primitive
Use Quick Pick or flowchart. State: "This is a [PRIMITIVE] because [ONE REASON]."

### 3. Render ASCII
- Max 20 lines
- Use: `─ │ ┌ ┐ └ ┘ ├ ┤ ┬ ┴ ┼ ← → ↑ ↓ ● ○ █`
- Label with user's domain terms

### 4. Verify
"Does this capture the structure? What's missing?"

---

## Output Structure

```
[1-line context]

┌─────────────────────────────────────┐
│     WHAT [USER/ISSUE] ASSUMES:      │
├─────────────────────────────────────┤
│  [Their mental model]               │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│          ACTUAL STATE:              │
├─────────────────────────────────────┤
│  [Reality]                          │
│     ↑ key difference                │
└─────────────────────────────────────┘

Primitive: [NAME] — "[reason]"

[ASCII DIAGRAM]

┌─────────┬────────────┬─────────────┐
│ Aspect  │  Assumed   │   Actual    │
├─────────┼────────────┼─────────────┤
│ X       │ state A    │  state B    │
└─────────┴────────────┴─────────────┘

★ Insight ─────────────────────────────
[Transferable lesson]
───────────────────────────────────────
```

---

## Constraints

- Emoji sparingly: 🟣🔵🟢 for state, ✓✗⚠️ for status
- Nested concepts: 3-space indent
- Pointer annotations: `↑` or `←` with 1-line labels
- Never: prose paragraphs first, box soup, mixed styles (`+--+` with `┌──┐`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mahidalhan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
