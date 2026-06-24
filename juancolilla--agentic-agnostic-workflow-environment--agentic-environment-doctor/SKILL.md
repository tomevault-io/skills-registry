---
name: agentic-environment-doctor
description: Audit, diagnose and repair a tool-agnostic AI workflow that has drifted over time. Use whenever the user complains that their AGENTS.md / CLAUDE.md / GEMINI.md / .cursorrules / skills "have grown messy", "are out of sync", "contradict each other", "are bloated", or that they want to clean up, audit, reconcile, deduplicate, defragment or reset their AI configuration. Also trigger when they ask to "check why my agent is ignoring rules", "find duplicate skills", "detect drift", "consolidate", "find stale docs", "validate AGENTS.md", "lint my skills", "what's in my CLAUDE.md", "merge several CLAUDE.mds into AGENTS.md", or to find documents whose code references have moved. This is the maintenance counterpart to agentic-environment-bootstrap — bootstrap creates, doctor heals. Use when this capability is needed.
metadata:
  author: JuanColilla
---

# Agentic Environment Doctor

Audits and repairs an AGENTS.md-based AI workflow after months of drift.
Inventories every config and skill file, clusters rules by intent, detects
the well-known drift symptoms, and proposes a reconciliation diff that the
user approves before any destructive change.

## When to use this skill

The user has an existing setup and complains:

- "My agents are ignoring rules I know are written somewhere."
- "I have three CLAUDE.md files and one of them contradicts the others."
- "My skills are duplicated across `~/.claude/skills/` and project skills."
- "I want to migrate from Claude-only to AGENTS.md without losing what I've
  written."
- "The `docs/` articles keep going stale; nobody updates `last_verified`."
- "Where is X configured?" — they don't know themselves anymore.

If they want to **start from scratch**, use the sibling skill
`agentic-environment-bootstrap`. This skill is for fixing what already exists.

## Mental model

Drift in an AI workflow shows up in five places. The doctor checks all five
in order; each step is read-only until the user approves a fix.

| # | Symptom | Detector |
|---|---|---|
| 1 | Truth fragmented across multiple files | `scripts/inventory.sh` |
| 2 | Adapters that are no longer adapters (CLAUDE.md > 3 lines) | `scripts/audit.py` |
| 3 | Skills bloated, duplicated, or undiscoverable | `scripts/audit.py` |
| 4 | DESIGN.md / ARCHITECTURE.md degraded into per-screen / dead | `scripts/audit.py` |
| 5 | Live library articles stale (code moved, articles didn't) | `scripts/docs-staleness.py` |

The full taxonomy of symptoms and what each one means is in
`references/drift-symptoms.md`. Read that file before judging a finding —
some symptoms are noisy on their own and only matter in combination.

## The doctor protocol

Always run these phases in order. Never skip Phase 0.

### Phase 0 — Read-only inventory

Run `scripts/inventory.sh` from the user's home dir or project root. It
emits a table:

```
path                                 type           lines  modified     status
/Users/me/.claude/CLAUDE.md          adapter?       127    2026-04-12   DRIFT (>3 lines)
/Users/me/.codex/AGENTS.md           agents.md      48     2026-03-01   ok
/Users/me/Projects/foo/AGENTS.md     agents.md      210    2026-04-20   bloated (>200 lines)
/Users/me/Projects/foo/CLAUDE.md     adapter        3      2026-04-20   ok
/Users/me/Projects/foo/.cursorrules  legacy         55     2025-09-10   migrate to AGENTS.md
~/.claude/skills/foo/SKILL.md        skill          640    2026-02-01   bloated (>500 lines)
```

Show the user this table verbatim. **Do not edit anything yet.** The
inventory is the conversation starter.

### Phase 1 — Cluster by intent

Read each file flagged for review and categorise its rules by domain:
networking, auth, UI, build, security, code style, git workflow, etc.
Produce a small markdown report grouping rules by domain across files,
showing where each one lives now. Same rule appearing in two files = a
de-dup target. Conflicting rules = a reconciliation target.

This step is **manual reasoning** by the agent. It is the high-value part
of the audit — the scripts cannot do it.

### Phase 2 — Reconcile to a single source of truth

For each cluster, decide where the rule belongs:

- **Cross-project preference** (style of response, language, commit format):
  goes to `~/.agents/AGENTS.md` (user level).
- **Project rule shared with the team** (build commands, code style,
  invariants): goes to `<root>/AGENTS.md`.
- **Personal override for this project** (local paths, experimental
  preferences): goes to `<root>/.agents/AGENTS.md` (gitignored).
- **Tool-specific behavior** (Claude Code allowed-tools, Cursor globs):
  goes into a skill's `x-<tool>` namespace, never inline in the adapter.
- **Bulk knowledge / context**: extract to a live-library article in
  `docs/Articles/` — instructions don't belong in AGENTS.md once they
  exceed the contract surface.

The reconciliation playbook with concrete decision rules is in
`references/reconciliation-playbook.md`.

### Phase 3 — Adapters back to thin

For every `CLAUDE.md` / `GEMINI.md` longer than 3 lines:

1. Confirm every rule has been moved to AGENTS.md or a skill.
2. Replace the file with the canonical 3-line adapter.

If the adapter contained Claude-Code-specific behavior (tool restrictions,
hooks), that behavior is **not** rules — it's a configuration. Move it to
`settings.json` (use the `update-config` skill). Memory is not the place
for behaviors the harness has to enforce.

### Phase 4 — Skills hygiene

For each skill flagged:

- **>500 lines:** split detail into `references/<topic>.md`, leave SKILL.md
  with a summary and pointers.
- **Duplicated across levels:** keep the skill at its broadest applicable
  level (user > project > project-user). Delete the others.
- **Description not "pushy":** rewrite description to include user phrases
  the skill should match plus an explicit "trigger even if user doesn't
  say X" — see `references/drift-symptoms.md` for the rationale.
- **Inline tool-specific behavior:** move to `x-<tool>` namespace or
  sibling file.

### Phase 5 — Doc freshness

Run `scripts/docs-staleness.py` against the project's live-library
directory (`docs/Articles/` and `docs/Decisions/`). It reports
articles whose `code_refs` have commits newer than `last_verified`.
Either:

- Verify the article still describes reality and bump `last_verified` to
  today.
- Mark it `status: deprecated` and note the supersession.
- Update its content to reflect the new code.

The script does not auto-bump dates — verification is a human/agent decision,
not a clock.

### Phase 6 — Propose a diff, apply only on approval

Summarise every recommended change as a diff. The user approves or rejects
per-hunk. Apply, then re-run `scripts/inventory.sh` and `scripts/audit.py`
to confirm the system is clean.

Never apply destructive moves automatically. The skill's value is in the
**diagnosis**; the user retains control of the cure.

## What this skill never does

- **Never deletes a file without showing its content first.** Even if
  flagged for removal.
- **Never auto-bumps `last_verified`** — that defeats the point of the
  staleness check.
- **Never overwrites a CLAUDE.md** without showing the full original first.
- **Never modifies project-user or `.gitignored` files** without an explicit
  "yes, that one too".
- **Never commits.** Final step is staged changes; the user commits.
- **Never edits `settings.json` directly** — invokes the `update-config`
  skill for harness behaviors, because those are managed declaratively.

## Output format for findings

When summarising the audit, use this structure so the user can act on it
quickly:

```
## Audit summary
- N files inventoried (M flagged)
- K skills (J flagged)
- D stale docs

## Critical
1. <one-line description> — file:line — proposed action

## Warnings
1. <one-line description> — file:line — proposed action

## Suggestions
1. <one-line description> — file:line — proposed action

## Reconciliation diff
<diff blocks the user can approve hunk-by-hunk>
```

## Reference & script map

- `references/drift-symptoms.md` — Every symptom, why it matters, how to
  weigh it. Includes partial-bootstrap residue (dir where a symlink was
  expected, suspicious nested symlinks, broken symlinks, orphan `.skill`
  packages, skill-count mismatch) and the recognition that the
  inverted-layout variant is **not** drift.
- `references/reconciliation-playbook.md` — Step-by-step plays for the
  common drift scenarios, including **Play G** for recovering from a
  partial bootstrap (the most common real-world drift).
- `references/invariants-pattern.md` — How invariants in ARCHITECTURE.md
  should be added (the second-error rule), and what makes a *good* invariant.
- `scripts/inventory.sh` — List every relevant config/skill/doc file with
  size, mtime and quick status. Now also reports **system markers**
  (`.codex-system-skills.marker`, `.skill-lock.json`, `.system/`) so
  tool-managed subtrees are visible and won't be confused with drift.
- `scripts/audit.py` — Heuristic detector for drift symptoms (Python
  stdlib only, no install). Detects partial-bootstrap residue, broken
  symlinks, orphan `.skill` packages, and skill-count mismatch across
  tools; honours system markers and the inverted layout as well-formed.
- `scripts/docs-staleness.py` — Compare `code_refs` mtime vs `last_verified`
  using `git log` (Python stdlib + git).

---
> Source: [JuanColilla/Agentic-Agnostic-Workflow-Environment](https://github.com/JuanColilla/Agentic-Agnostic-Workflow-Environment) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
