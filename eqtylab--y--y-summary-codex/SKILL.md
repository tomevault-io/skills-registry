---
name: y-summary-codex
description: Summarize Codex session JSONL logs into y-schema SummaryRoot outputs with evidence mapping and validation, then attach the summary as a git note under y-codex-session-summary. Use for building or updating the Codex session summarization pipeline or when asked to transform a Codex session log into structured summary artifacts. Use when this capability is needed.
metadata:
  author: eqtylab
---

# y-summary-codex

## Purpose
Build the summarization pipeline that reads Codex session `.jsonl` logs and emits validated `SummaryRoot` JSON, then attaches the final summary as a git note in namespace `y-codex-session-summary`.

## Inputs
- Session log path (absolute or relative to `~/.codex/sessions`).
- Output directory for `summary.json`.

## Non-negotiable Guardrails
- **YOU must NOT summarize your own progress creating the summary of the work you are reviewing.**
- **YOU must only summarize the work performed in the session being reviewed.**

## Required Workflow
Follow these steps in order and keep outputs deterministic.

1. Parse session JSONL
- Read line-delimited JSON.
- Normalize core event model: timestamp, type, actor/source, payload, call id, line index.
- Preserve order using `(timestamp, line_index)`.

2. Build evidence map first
- Create deterministic evidence IDs from `(timestamp, event_type, snippet, line_index)`.
- Store evidence tuples with: timestamp, event, snippet, source.
- Do not use assistant reasoning as sole evidence for facts.

3. Extract all 11 sections
- Implement extractors using the methods in `references/section-methods.md`.
- Preserve fact vs inference (set `basis` accordingly).
- Every evidence-backed claim must include `evidence_refs`.
- If data is missing, emit empty structures (not omitted) with low-confidence claims only when explicitly allowed.

4. Assemble SummaryRoot
- Set `schema_version` to `y-schema::CURRENT_SCHEMA_VERSION`.
- Populate all sections (never omit required sections).
- Link evidence by ID only.

5. Validate before writing outputs
- Use `y-schema::parse_summary` or `validate_summary` and fail with actionable errors (JSON path + reason).
- Prefer `validate_summary` for multi-error reporting.
- Always run the CLI validator `y validate summary` against the final JSON before writing the git note.
- If CLI validation fails, stop and surface the error paths; do not write outputs or notes.
- Keep library validation too; CLI validation is the required final gate.

6. Write summary.json
- Write `summary.json` from `SummaryRoot` only.
- Do not emit human-readable outputs unless explicitly requested by the user.

7. Attach git note
- Write the final summary JSON or a compact pointer (see `references/output-contract.md`) as a git note under namespace `y-codex-session-summary`.
- Use existing git helper in `/Users/ramos/yapp/y/src/git.rs` if applicable.

## Determinism Rules
- Sort lists by first evidence timestamp, then stable tie-breaker (line index or path).
- Hash-based IDs must be reproducible from content, not random.
- Avoid nondeterministic iteration over hash maps.

## When to read extra references
- Use `references/section-methods.md` for extraction heuristics and section-specific rules.
- Use `references/output-contract.md` for output formatting and inbox summary structure.
- Use `/Users/ramos/yapp/y-skills/y-search-codex/SKILL.md` if you need to locate or search sessions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eqtylab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
