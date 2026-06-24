---
name: civ-finish-quotes
description: Add a Civilization-style ceremonial quote when a substantial task is truly complete. Use this whenever the user or agent is wrapping up a real deliverable such as a feature, refactor, analysis, design doc, process change, report, or writing task, even if they do not explicitly ask for a quote; skip short replies, tiny fixes, and unfinished work. Use when this capability is needed.
metadata:
  author: huxiuhan
---

# Civ Finish Quotes

Use this skill as a final completion ritual after a real piece of work is finished.

This skill is for the last step of a substantial task, not for ordinary chat. It should feel like a Civilization technology or wonder completion line: brief, ceremonial, and anchored by a real quote with an author and source.

## Compatibility

- requires local `python3`
- expects access to this skill directory, especially `scripts/` and `assets/quotes/approved_quotes.jsonl`

## Trigger Gate

Default behavior: trigger this skill for almost all task closures that produced a real result.

Use this lenient gate:

1. The work has some concrete output.
   Examples: code/doc updates, analysis conclusion, decision, plan, verification, checklist completion.
2. The work is presented as done for this turn.
   Examples: "finished", "completed", "done", "ready", "交付", "完成", "发布".

Only skip this skill for clear non-completion micro replies:

- casual replies
- tiny fixes
- a single command answer
- brainstorming that has not been implemented
- tasks that ended with uncertainty or partial progress

## Runtime Flow

When the trigger gate passes:

1. Summarize the finished task into a small JSON payload.
2. Call the local render script.
3. If it returns `no_match`, say nothing extra and end normally.
4. If it returns `ok`, read `quote_text` and `needs_translation`.
5. If `needs_translation=true`, translate `quote_text` into the user's language in the final reply.
6. Compose the final ceremonial block in the user's language with a fixed divider line.

## Hard Rules

- Never invent, paraphrase, or "write something quote-like" yourself.
- Only output a completion quote when the renderer returns `status="ok"`.
- The final quote body must come from the renderer's returned `quote_text`.
- The attribution must come from the renderer's returned `author` and `source_title`.
- If the renderer returns `no_match`, do not add a fallback quote, a hand-written ceremonial line, or a pseudo-quote.

## Request Payload

Use this structure:

```json
{
  "task_summary": "Implemented the new quote selection pipeline and documented the curation flow.",
  "deliverable_type": "code",
  "completion_class": "engineering",
  "completion_mode": "build",
  "keywords": ["pipeline", "selection", "curation", "script"],
  "user_language": "zh-CN",
  "recent_quote_ids": []
}
```

### Completion Classes

- `science`: analysis, investigation, model design, research, root-cause work
- `engineering`: implementation, refactor, tooling, architecture, shipping a system
- `governance`: process, policy, permissions, stability, ownership, organization
- `art-thought`: writing, naming, concept shaping, knowledge organization, design rationale

### Completion Modes

- `breakthrough`
- `build`
- `organization`
- `insight`

## Render Command

Run:

```bash
python3 ./scripts/render_finish_quote.py --library ./assets/quotes/approved_quotes.jsonl --input-json '<JSON_PAYLOAD>'
```

The renderer returns:

- `{"status":"ok","id":"...","quote_text":"...","needs_translation":true|false,"author":"...","source_title":"...","divider":"----------","selection_mode":"ranked|fallback","selection_profile":{...},"match_reason":{...},"rejected_candidates":[...] }`
- or `{"status":"no_match"}`

Notes:

- `selection_mode=fallback` means the request looked like a completed task, but keyword matching was sparse; the renderer still selected a domain/mode-consistent quote to reduce misses.
- The selector enforces a relevance floor; if relevance is too low it returns `no_match`.
- The renderer keeps a small local history file and tries to avoid reusing the same quote within a rolling 24-hour window when other good candidates exist.
- By default it tries to store that history in the user cache directory, and falls back to a repo-local `.cache/` when the runtime cannot write there.
- For design-document style tasks, governance/organization requests are remapped toward engineering/build semantics to reduce topic drift.
- For non-sensitive tasks, high-risk themes (race/colonial trauma/war-massacre style language) are filtered out by default.

## Output Contract

The final quote block must:

- start with the fixed divider line `----------`
- show only the user-language version of the quote body
- always include author
- always include source
- use only renderer-returned fields for quote body and attribution
- avoid extra commentary after the quote block

Use this shape:

```text
----------

“<translated quote>”
—— <author>，《<source title>》
```

---
> Source: [huxiuhan/civ-finish-quotes](https://github.com/huxiuhan/civ-finish-quotes) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
