---
name: copilot-migration
description: >- Use when this capability is needed.
metadata:
  author: OxyFlax
---

# Copilot → Claude Code migration

Bring a user's GitHub Copilot conversation history into Claude Code in three steps:
**extract** the raw conversations from disk → **document** them as readable Markdown
plus an index → **continue** the most recent one.

GitHub Copilot stores chat history locally in VS Code's storage (two different
on-disk formats, see the reference). Nothing is fetched from the network.

## When to use
- "extract / recover / export / back up my Copilot conversations or chat history"
- "I'm migrating from GitHub Copilot to Claude Code"
- "document / summarize what I did with Copilot"
- "continue my last Copilot conversation" / "pick up where Copilot left off"

## What's bundled (resolve paths from this skill's directory)
- `scripts/extract_copilot.py` — standalone, **no-dependency** extractor. Discovers
  Copilot storage across editors/OSes, decodes **both** on-disk formats, dedupes
  sessions resumed across windows, and writes one faithful Markdown transcript per
  conversation + a `manifest.json`. **Run it — do not re-implement the parsing.**
- `references/storage-and-formats.md` — exactly where Copilot stores chats per
  OS/editor and how the two formats work. Read it if discovery finds nothing, or to
  explain the mechanics to the user.

## Procedure

### 1 — Extract (deterministic, read-only)
List first, then extract:
```bash
python3 <skill>/scripts/extract_copilot.py --list           # discover + preview
python3 <skill>/scripts/extract_copilot.py --out <out-dir>  # write transcripts
```
- Pick `<out-dir>`: if the project has a `docs/` directory, use
  `docs/copilot-history/`; otherwise `./copilot-history/`.
- To scope to one project, add `--filter <substring>` (matches the workspace path
  or conversation content). Use `--no-reasoning` to drop the model's "thinking".
- If `--list` finds **nothing**: read `references/storage-and-formats.md`, check the
  candidate paths it printed to stderr, and ask the user which editor/host they used
  Copilot in (desktop vs. remote SSH/WSL/Cursor change the location).
- Output: `<out-dir>/transcripts/*.md` (faithful, verbatim) + `manifest.json`.
  **Skim `manifest.json`** (title/date/turns/files per conversation) to decide what
  matters — don't blindly re-read every large transcript.

### 2 — Document (you write these)
In `<out-dir>`:
- `sessions/<same-filename>.md` — a tight summary per conversation: title, date,
  goal, what was done, files changed, key decisions, outcome, open threads. Read the
  full transcript for large/important ones; the manifest + first/last turns suffice
  for trivial ones. If there are many, fan out subagents (one per transcript).
- `README.md` — an index: a 2–3 sentence intro, a **chronological table**
  (date | linked conversation | one-line outcome), and a grouping **by theme**.

### 3 — Continue the most recent conversation
This is usually the user's real goal.
- The latest conversation is the **last row of `manifest.json`** — it is sorted by
  most-recent activity (`last_ts`), so the final row is the conversation the user
  used most recently (each row also carries `first_ts` if you want start-date order).
- Read its full transcript. Cross-reference against the **current repo state**
  (`git log`, the files it touched) to separate what actually landed from what was
  only discussed.
- Write `<out-dir>/CONTINUE_HERE.md`: where it left off (exact stop point), what
  landed vs. discussed, verified vs. unverified, open problems, and a prioritized
  next-steps checklist referencing concrete files/commands.
- Then offer to do the next step.

## Notes & guarantees
- The extractor is **read-only** on Copilot storage and writes only under `--out`.
  No network, no extra packages (standard library only). Safe to run anywhere.
- A conversation **resumed across several editor windows** is saved as multiple files
  sharing one sessionId (later windows replay earlier turns); the script merges and
  dedupes turns by prompt content, keeping the most complete copy.
- The division of labor is deliberate: the **script** does the exact, verbatim
  extraction (no hallucination risk); **you** do the summarizing and judgement.
- This skill is distributable: it has no hard-coded paths or project assumptions —
  discovery and `--filter` are fully general.

---
> Source: [OxyFlax/AI](https://github.com/OxyFlax/AI) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
