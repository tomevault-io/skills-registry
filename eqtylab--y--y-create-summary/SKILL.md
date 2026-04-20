---
name: y-create-summary
description: Create a concise provenance summary for a commit by reading commit metadata and Codex session context, then writing note.json with intent, constraints, and actions. Use when you need to mint commit-level summaries from commit.json, CODEX.md, and Codex session logs. Use when this capability is needed.
metadata:
  author: eqtylab
---

## Inputs

- Accept a workspace path.
- Read `commit.json` and `CODEX.md` from the workspace root.
- Read session logs from `~/.codex/sessions` (or `$CODEX_HOME/sessions`).

## Workflow

1. Read `commit.json` and extract `hash`, `message`, `timestamp`, `repoPath`, and `changedFiles` when present.
2. Read `CODEX.md` and identify referenced session paths.
3. Parse the referenced JSONL sessions and select evidence that supports summary construction:
   - User requests that describe goals or problems.
   - Tool calls and shell actions tied to implementation, testing, or git operations.
   - Mentions of commit hash prefixes, commit message text, or changed file names.
   - Messages close to the commit timestamp.
4. Build `sessionSummary` from evidence:
   - `intent`: the user goal.
   - `constraints`: explicit requirements or limits.
   - `actions`: concrete implementation and validation steps.
5. Write `note.json` in the workspace root.
6. If evidence is sparse, infer conservatively from `commit.json` and mark uncertain details in `summary` without speculation.

## Output

Write `note.json` with required fields:

- `hash`
- `message`
- `author`
- `timestamp`
- `summary`
- `sessionSummary.intent`
- `sessionSummary.constraints`
- `sessionSummary.actions`

Optional fields:

- `sessions` (relative paths under `~/.codex/sessions`)
- `changedFiles`
- `agents` (use `"codex"`)
- `decisions`
- `highlights` (brief redacted paraphrases only)

## Safety

- Do not modify repository source files.
- Restrict writes to workspace output files.
- Do not copy raw session transcripts into `note.json`.
- Redact sensitive values and keep summaries minimal.

## Quality Bar

- Make `summary` explain why the change exists, not only what changed.
- Keep `constraints` specific and actionable.
- Keep `actions` chronological and concrete.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eqtylab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
