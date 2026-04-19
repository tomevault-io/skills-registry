---
name: accessing-notebooklm-mcp
description: Operates NotebookLM through MCP tools to authenticate, create/list/rename/delete notebooks, add/sync/delete sources (URL, text, Drive), run research_start workflows, ask notebook_query questions, export source content, and generate studio artifacts (audio/video/infographic/slides/reports/flashcards/quizzes/tables/mind maps). Use when the user mentions NotebookLM, MCP, notebooks, sources, Drive docs, web research, “summarize my sources,” “ask the notebook,” or creating overviews/slides/reports/flashcards/quizzes. Use when this capability is needed.
metadata:
  author: leonardoteodoroo
---

# Accessing NotebookLM via MCP

## When to use this skill

- User mentions **NotebookLM** or **MCP** access (e.g., “use NotebookLM MCP,” “connect to NotebookLM,” “query my notebook”).
- User wants to **create/list/manage notebooks** (list, create, rename, delete).
- User wants to **add sources** (URL/YouTube, pasted text, Google Drive doc/slides/sheets/pdf).
- User wants **web/Drive research** to find _new_ sources (notebooklm research workflow).
- User wants **answers strictly from sources already in a notebook** (notebook_query).
- User wants **exports/summaries** of sources (source_get_content / source_describe / notebook_describe).
- User wants **studio outputs**: audio/video overview, infographic, slide deck, report, flashcards, quiz, data table, mind map.

## Workflow

### Checklist

- [ ] Confirm authentication status (refresh tokens if needed).
- [ ] Identify the target notebook (existing by title/id, or create a new one).
- [ ] Choose the correct action lane:
  - [ ] Notebook admin
  - [ ] Source management (add/list/sync/delete)
  - [ ] Ask notebook (query existing sources)
  - [ ] Research (find new sources via web/Drive)
  - [ ] Studio artifact generation (requires explicit approval)
  - [ ] Destructive actions (requires explicit approval)
- [ ] Plan → Validate → Execute
- [ ] Summarize results + provide next-step options.

### Plan → Validate → Execute (required pattern)

**Plan**

- Restate the user goal in one sentence.
- Pick the minimal set of MCP calls needed.

**Validate**

- Verify notebook_id exists (or create it).
- Verify sources exist / are imported before querying.
- For Drive sync: run `source_list_drive` first.
- For destructive/studio actions: obtain explicit user approval before calling with `confirm=True`.

**Execute**

- Perform the MCP calls.
- Report the output IDs/URLs and what changed.

## Instructions

## 1) Authentication & session recovery

Use this flow whenever calls fail due to auth or right after running CLI auth.

1. Call `refresh_auth`.
2. If unsuccessful:
   - Prefer running the CLI in terminal: `notebooklm-mcp-auth` (see scripts/).
   - Then call `refresh_auth` again to pick up new tokens/cookies.
3. If CLI auth fails and the user explicitly provides cookie headers, use `save_auth_tokens` as a fallback.

**Rules**

- Do not proceed with notebook/source operations if auth is not valid.
- When unsure how to run a helper script, run it with `--help`.

---

## 2) Notebook selection & management

### Select a notebook

- If the user provides a `notebook_id`, use it.
- Else:
  1. Call `notebook_list` (default max_results is fine).
  2. Match by title (case-insensitive substring).
  3. If no clear match, create a notebook with `notebook_create` using a descriptive title.

### Common operations

- **List:** `notebook_list(max_results=...)`
- **Create:** `notebook_create(title=...)`
- **Details + sources:** `notebook_get(notebook_id=...)`
- **AI summary + suggested topics:** `notebook_describe(notebook_id=...)`
- **Rename:** `notebook_rename(notebook_id=..., new_title=...)`

### Delete (irreversible)

- Only call `notebook_delete(notebook_id=..., confirm=True)` after explicit user approval that includes “delete” intent.
- Before deleting, fetch context with `notebook_get` and summarize what will be removed.

---

## 3) Adding sources (URL, text, Drive)

### Add a URL or YouTube transcript

- Use `notebook_add_url(notebook_id=..., url=...)`.
- After adding, call `notebook_get` to confirm the source appears.

### Add pasted text

- Use `notebook_add_text(notebook_id=..., text=..., title=optional)`.

### Add Google Drive documents

- Use `notebook_add_drive(notebook_id=..., document_id=..., title=..., doc_type=doc|slides|sheets|pdf)`.
- Extract `document_id` from the Drive URL (the long ID in `/d/<ID>/` or similar).

**Validation tips**

- After adding sources, call `notebook_get` to obtain source IDs.
- For quick export of indexed text, prefer `source_get_content` over `notebook_query`.

---

## 4) Source inspection, export, and deletion

### Describe (AI summary + keyword chips)

- Use `source_describe(source_id=...)` to generate a short summary and keywords.

### Export raw indexed content (fast)

- Use `source_get_content(source_id=...)` to retrieve the original indexed text and metadata.

### Delete a source (irreversible)

- Only call `source_delete(source_id=..., confirm=True)` after explicit user approval.
- Confirm the correct source by title/type via `notebook_get` before deleting.

---

## 5) Asking questions about existing sources (NOT web search)

Use `notebook_query` only when the needed sources are already in the notebook.

**Steps**

1. Ensure sources exist:
   - `notebook_get` and capture relevant `source_ids` (or query all).
2. Ask:
   - `notebook_query(notebook_id=..., query=..., source_ids=[...optional...], conversation_id=...optional..., timeout=...optional...)`

**Follow-ups**

- Reuse `conversation_id` for iterative questioning.

**If responses time out**

- Narrow to specific `source_ids`.
- Increase `timeout` if the environment allows.

---

## 6) Research workflow (find NEW sources via web or Drive)

Use this lane when the user wants discovery, “find sources,” “deep research,” “search web,” or “search Drive.”

**Correct sequence**

1. `research_start(query=..., source=web|drive, mode=fast|deep, notebook_id=optional, title=optional)`
2. `research_status(notebook_id=..., task_id=optional, poll_interval=30, max_wait=300, compact=True)`
3. When status is completed:
   - `research_import(notebook_id=..., task_id=..., source_indices=optional)`

**Heuristics**

- Use `mode=fast` for quick results and iteration.
- Use `mode=deep` for comprehensive web-only research.
- After import, call `notebook_describe` to generate a notebook-level summary and suggested topics.

---

## 7) Drive source freshness & sync

When the user asks to “sync Drive docs” or “update sources”:

1. Call `source_list_drive(notebook_id=...)`.
2. Identify stale/out-of-date sources.
3. Ask for explicit approval to sync.
4. Call `source_sync_drive(source_ids=[...], confirm=True)`.
5. Confirm updates by re-running `source_list_drive` or `notebook_get`.

---

## 8) Chat configuration (goal + length)

Use `chat_configure` to steer style:

- `goal=default|learning_guide|custom`
- If `goal=custom`, provide `custom_prompt` (<= 10000 chars).
- `response_length=default|longer|shorter`

**Pattern**

- Configure once per notebook session unless the user changes requirements.

---

## 9) Studio artifact generation (requires explicit approval)

These calls require `confirm=True` only after the user explicitly approves generating the artifact.

### Available artifacts

- Audio overview: `audio_overview_create(...)`
- Video overview: `video_overview_create(...)`
- Infographic: `infographic_create(...)`
- Slide deck: `slide_deck_create(...)`
- Report: `report_create(...)` (use “Create Your Own” only with a custom prompt)
- Flashcards: `flashcards_create(...)`
- Quiz: `quiz_create(...)`
- Data table: `data_table_create(...)`
- Mind map: `mind_map_create(...)`

**Execution pattern**

1. Validate sources exist (via `notebook_get`), optionally limit `source_ids`.
2. Obtain explicit user approval.
3. Call the create function with `confirm=True`.
4. Poll `studio_status(notebook_id=...)` and return any URLs or artifact IDs.

### Delete a studio artifact (irreversible)

- Only after explicit user approval:
  - `studio_delete(notebook_id=..., artifact_id=..., confirm=True)`

---

## 10) Minimal call templates (copy/paste patterns)

### Auth refresh

```text
refresh_auth()
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/leonardoteodoroo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
