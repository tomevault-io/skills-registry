---
name: communication-style
description: >- Use when this capability is needed.
metadata:
  author: casper7995
---

# Communication style (transcript → learned rules)

Keep **`~/.cursor/rules/communication-learned.mdc`** (user-level, all projects) current using **transcript deltas** instead of full rescans. Do **not** edit **`~/.cursor/rules/communication-base.mdc`** (human-owned defaults).

Resolve `~` to the current user’s home directory for all paths below.

## Inputs

- **Transcript discovery (all workspaces):** Under **`~/.cursor/projects/`**, each immediate subdirectory is a Cursor project slug. For every `<slug>` where **`~/.cursor/projects/<slug>/agent-transcripts/`** exists, collect all **`*.jsonl`** files in that folder (non-recursive is typical; if files are nested one level deeper, search one level deep under `agent-transcripts` only).
  - Use **absolute paths** for every transcript file in later steps.
  - If **`~/.cursor/projects/`** is missing or no `agent-transcripts` folders exist, the transcript set is empty—still run index cleanup (workflow step 9) and respond appropriately.
- **Existing base rule (read-only):** `~/.cursor/rules/communication-base.mdc`
- **Existing learned rule (read/write):** `~/.cursor/rules/communication-learned.mdc`
- **Incremental index (user-level, all workspaces):** `~/.cursor/communication-style-index.json`  
  Transcript paths in the index are absolute, so one index correctly tracks every project’s `.jsonl` files.

## Workflow

1. Ensure **`~/.cursor/rules/`** exists.
2. Read **`~/.cursor/rules/communication-base.mdc`** if it exists (for context only; do not modify).
3. Read **`~/.cursor/rules/communication-learned.mdc`** if it exists; if missing, create it using the **Learned file template** below (preserve frontmatter).
4. Load the incremental index from **`~/.cursor/communication-style-index.json`** if present. If the JSON is invalid or missing `version`, treat as empty and use `version: 1` and `transcripts: {}`.
5. Discover all `*.jsonl` files across **every** `~/.cursor/projects/*/agent-transcripts/` directory (per **Inputs**). Deduplicate by absolute path if the same file could appear twice.
6. Process **only** transcripts where:
   - the absolute path is **not** in the index, or
   - the file’s current `mtimeMs` is **greater** than the indexed `mtimeMs`.
7. From those files, extract **only high-signal, reusable** communication preferences:
   - **Explicit** user instructions about how to write, plan, or structure answers.
   - **Recurring** corrections across threads (weak one-offs: require repetition or a clear “always / never” rule before promoting).
8. Merge into **`~/.cursor/rules/communication-learned.mdc`**:
   - Keep the **frontmatter** intact (`description`, `alwaysApply: true`).
   - Preserve exactly these section headings (create empty sections if missing):
     - `## Plan mode`
     - `## General coding replies`
     - `## Shared preferences` (optional; use for habits that apply to both surfaces)
   - **Plain `-` bullets only** under each section (no nested metadata, evidence tags, or confidence labels).
   - **Update matching bullets in place**, **add** only net-new bullets, **deduplicate** semantically similar lines.
   - Cap **total** learned bullets across all sections at **18** (drop or merge lowest-value items if over cap).
9. Write back the incremental index to **`~/.cursor/communication-style-index.json`**:
   - `version: 1`
   - `transcripts`: map absolute path → `{ "mtimeMs": number, "lastProcessedAt": "<ISO8601>" }`
   - Remove entries for transcript files that no longer exist.

## Learned file template (when creating new)

```markdown
---
description: Learned communication preferences for plan mode and general coding (updated by communication-style skill).
alwaysApply: true
---

# Communication (learned)

## Plan mode

-

## General coding replies

-

## Shared preferences

-
```

Remove the placeholder `-` lines once you add real content, or replace them with real bullets.

## Inclusion bar

Keep a bullet only if **all** are true:

- Actionable in future sessions for **plan writing** and/or **normal agent replies**.
- Stable (not a one-off task or transient error).
- **Explicit** or **repeated** across transcripts (broad user-stated rules count as explicit).
- Non-sensitive (no secrets, tokens, private personal data).

## Exclusions

Never store:

- Secrets, credentials, API keys, private personal data.
- One-off task instructions (“fix line 42 today”).
- Transient details (branch names, one-time CI errors) unless the user framed them as a **standing** rule about how you want communication.

## Output when nothing to do

If no transcript qualified for processing or no high-signal preferences were found, still save the index if mtimes were checked. Respond to the user with exactly:

`No high-signal communication-style updates.`

Otherwise give a **brief** summary of what changed in the learned file.

## Linguistic hints (weak signals)

Treat phrases like “too long”, “too verbose”, “just the steps”, “more context”, “answer first”, “tl;dr”, “use headings”, “don’t ask me that every time” as **weak** until they recur or the user states a general rule—then promote to bullets in the appropriate section (**Plan mode** vs **General coding replies** vs **Shared preferences**).

## File-level incrementality

Processing is **per transcript file** by **mtime**: when a file changes, re-read that **entire** `.jsonl` for that conversation. There is no line-level index.

---
> Source: [casper7995/cursor-plugin-communication-style](https://github.com/casper7995/cursor-plugin-communication-style) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
