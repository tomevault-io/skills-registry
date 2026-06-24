---
name: gemini-subagents
description: Use when building a website or generating a large amount of frontend/code and you want a manager-and-workers setup — an orchestrator agent (e.g. Claude) directs while cheap, fast Gemini workers do the bulk generation. Covers headless Gemini CLI workers and an Antigravity handoff to reach Gemini 3.5 Flash on consumer accounts. Triggers include "use gemini as subagents", "orchestrate gemini", "manager and workers", parallel section generation, or splitting a website build across multiple model calls.
version: 1.0.0
license: MIT
metadata:
  author: Taher Farg
  homepage: https://github.com/taherfarg/claude-antigravity-subagents
---

# Gemini Subagents

## Overview
A **manager/worker** pattern for building frontends. The **orchestrator** (Claude, or any capable agent) owns taste and architecture: it sets the design direction, splits the work, writes precise specs, and reviews + audits every result. **Gemini workers** do the bulk code generation — fast and cheap. Quality control stays with the manager because fast models drift toward generic ("slop") output.

Two ways to run the workers:
- **Mode A — headless Gemini CLI** (automated): the orchestrator auto-dispatches `gemini -p` calls. Scriptable and parallel.
- **Mode B — Antigravity handoff** (human-in-the-loop, Gemini 3.5 Flash): the orchestrator writes spec files; a human runs Antigravity (which exposes Gemini 3.5 Flash on consumer accounts) to generate; Antigravity writes code into the same workspace; the orchestrator reads + audits it.

## Models
| Model ID | Where | Use for |
|---|---|---|
| `gemini-3-flash-preview` | Gemini CLI | Default headless worker (this IS "Gemini 3 Flash"). Bulk sections/components. |
| `gemini-2.5-flash` | Gemini CLI | Stable fallback worker. |
| `gemini-2.5-pro` | Gemini CLI | Hard / visually-critical parts (hero, brand, tricky logic). |
| "Gemini 3.5 Flash" | Antigravity | Best worker quality on consumer accounts — but interactive only (Mode B). |

**ID gotchas (verify — these change over time):** in the Gemini CLI, `gemini-3.5-flash`, `gemini-3-flash`, and `gemini-flash-latest` return `ModelNotFoundError`. The real Gemini 3 Flash ID is `gemini-3-flash-preview`. Gemini 3.5 Flash is currently reachable only through Antigravity. Confirm any ID:
```bash
gemini -m <model-id> -p "ok"   # "ModelNotFoundError" = wrong ID
```
> Google announced the Gemini CLI stops serving Google One / unpaid tiers (June 18, 2026). On consumer accounts, Mode B (Antigravity) becomes the path to the newest models. Re-check current state before relying on Mode A.

## Mode A — headless Gemini CLI (automated)
```bash
# stdout mode (manager reviews, then writes the file) — SAFEST default
gemini -m gemini-3-flash-preview -p "<spec>. Output ONLY raw code to stdout. Do NOT use tools or write files. No markdown fences, no explanation."

# autonomous mode (Gemini writes files itself) — scope the cwd, auto-accept actions
cd <scoped-folder> && gemini -m gemini-3-flash-preview -y -p "<spec>; write the result to ./path/file.tsx"
```
- `-p` headless · `-m` model · `-y` auto-accept tool/file actions
- **Parallel:** dispatch several `gemini -p` calls as background jobs (one per section), then collect + review all outputs.
- Strip noise lines (`true color`, `ripgrep`, `Shell cwd was reset`) from captured output before parsing.

## Mode B — Antigravity handoff (Gemini 3.5 Flash)
Antigravity is an agentic IDE, not a headless CLI, so it can't be auto-dispatched. The orchestrator and Antigravity **share the workspace filesystem**; a human is the runner.

1. **Orchestrator** writes a spec to `specs/<section>.md` (use `spec-template.md`) — design tokens + bans + exact files baked in.
2. **Human** opens Antigravity in the project, sets the model to **Gemini 3.5 Flash** (`/model` command → pick it, or the model picker), and says: *"Implement specs/<section>.md."*
3. **Antigravity** writes the code files into the repo.
4. **Human** says "done" — the orchestrator reads the files directly (no pasting).
5. **Orchestrator** reviews + audits, then writes the next spec or a fix-list spec.

## The director's job (non-negotiable)
- Lock ONE aesthetic + exact font/color/spacing tokens **before** any code.
- Decompose the build into independent sections.
- Bake the rules into every spec — workers do NOT see the orchestrator's skills.
- Review every output; reject slop; never ship a worker's first draft unchecked.

## Worker spec must include
Locked aesthetic · exact font/color/spacing tokens · explicit bans (no Bootstrap blue `#007bff`, no inline event handlers, no templated hero+3-cards, no placeholder/TODO code, no lorem ipsum) · exact file path(s) to write · a short acceptance checklist · the output contract (stdout-only, or the file to write).

## Common mistakes
| Mistake | Fix |
|---|---|
| Using `gemini-3.5-flash` in the CLI | That ID 404s. Use `gemini-3-flash-preview`, or Antigravity for true 3.5 Flash. |
| No "output only" instruction (Mode A) | Gemini tries tools, edits stray files, rambles. Always state the output contract. |
| Running `-y` from a broad cwd | It may touch unrelated files. Scope to a target folder. |
| Trusting worker output as-is | Fast models default to generic styling. Always review + audit before integrating. |
| Vague specs | Bake in tokens + bans, or you get slop back. |
| Expecting Antigravity to be headless | It's interactive. Use the spec-file handoff loop (Mode B). |

## Quick reference
- Worker (CLI): `gemini -m gemini-3-flash-preview -p "<spec + output contract>"`
- Hard part: `gemini-2.5-pro`. True Gemini 3.5 Flash: Antigravity (Mode B).
- Always: orchestrator locks tokens → workers generate → orchestrator reviews + audits.

---
> Source: [taherfarg/claude-antigravity-subagents](https://github.com/taherfarg/claude-antigravity-subagents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
