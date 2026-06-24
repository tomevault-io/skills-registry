---
name: y-graph-codex
description: Build a minimal React Flow-ready graph from SummaryRoot JSON and attach it as a git note under y-codex-session-graph. Use when this capability is needed.
metadata:
  author: eqtylab
---

# y-graph-codex

## Purpose
Read SummaryRoot JSON and emit a minimal graph representation focused on user messages, decisions, files, artifacts, and objective/outcome. Attach the graph JSON as a git note under namespace `y-codex-session-graph`.

## Inputs
- SummaryRoot JSON path (absolute or relative).
- Output directory for `graph.json`.

## Required Output
- `graph.json`: GraphRoot JSON.
- Git note: write full `graph.json` body under namespace `y-codex-session-graph` for the commit.

## Graph Schema (GraphRoot)
```
{
  "schema_version": "1",
  "nodes": [
    { "id": "...", "type": "...", "data": { "label": "...", "detail": "...", "timestamp": "...", "evidence_refs": ["..."] } }
  ],
  "edges": [
    { "id": "...", "source": "...", "target": "...", "type": "...", "label": "..." }
  ],
  "meta": { "commit": "..." }
}
```
- This structure is React Flow-ready (nodes/edges only, no positions).

## Determinism Rules
- Use stable ordering: by timestamp when present, then by original index.
- Use deterministic IDs, e.g. `sha256("<kind>|<timestamp>|<label>|<index>")`.
- Do not use random values.

## Nodes to Create
- `message` nodes from `user_message_ledger.messages`.
- `decision` nodes from `decisions_and_tradeoffs.decisions`.
- `file` nodes from `files_edited_and_why.files`.
- `artifact` nodes from `structured_output_artifacts.artifacts` plus JSON/markdown summary pointers.
- `objective` and `outcome` nodes from `executive_summary`.

## Edges to Create
- `message_sequence`: message[i] -> message[i+1].
- `message_to_decision`: if decision rationale evidence references a user evidence record that matches message timestamp or snippet overlap.
- `decision_to_file`: if decision rationale evidence and file.why share any evidence id.
- `decision_to_artifact`: if decision rationale mentions artifact location in text.
- `objective_to_decision` and `decision_to_outcome` for overall flow.

## Validation
- Ensure unique IDs.
- Ensure all edges reference existing node IDs.
- Ensure at least one node exists; otherwise fail with an actionable error.

## Notes
- Include evidence refs in node `data.evidence_refs` where they exist.
- Keep node `label` short and readable; put long text in `detail`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eqtylab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
