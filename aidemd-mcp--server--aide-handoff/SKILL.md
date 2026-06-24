---
name: aide-handoff
description: > Use when this capability is needed.
metadata:
  author: aidemd-mcp
---

# /aide:handoff — Session Handoff

Record current pipeline state to `.aide/session.aide`. This skill owns the orchestration; the `aide-spec-writer` agent owns the actual file write (it transcribes orchestrator-supplied state into the canonical format). The `aide-maintainer` agent (NOT this skill) owns deletion at feature close. See [session.aide spec](../../../.aide/docs/session-aide.md) for the file format.

## Hard constraints

- **You do NOT write `.aide/session.aide` yourself.** The actual file write belongs to `aide-spec-writer`. This skill describes the orchestration that surrounds the delegation.
- **You do NOT invent content.** The invoking agent (orchestrator or other) holds the conversation context — current stage, settled decisions, where work paused, anti-regression invariants. That context is the content source. If the conversation doesn't supply it, ask before invoking the skill — never fabricate state.
- **You do NOT delete `.aide/session.aide`.** That belongs to `aide-maintainer` at feature close. This skill is exclusively for create/update.

## Checklist

- [ ] **Detect operation mode.** Read whether `.aide/session.aide` exists:
  - File missing → **CREATE** mode (first handoff for the in-flight feature)
  - File exists → **UPDATE** mode (apply a surgical diff to the existing log)
- [ ] **Gather content from conversation context.** Before delegating, identify the specific pieces `aide-spec-writer` needs:
  - **For CREATE:** feature intent (one line for the frontmatter `intent:` field); state summary (current stage + what was just done + what's blocked); `## Where this cycle stopped` content (the next agent's instructions); architectural decisions settled (numbered list, if any); anti-regression invariants (bullet list, if any); process discipline notes (optional); open questions (optional)
  - **For UPDATE:** change description (what transition occurred); section-targeted edits (e.g. "append decision #4: ...", "rewrite `## Where this cycle stopped` to: ..."); numbered references to preserve (decision #N stays stable across edits)
- [ ] **Delegate to `aide-spec-writer`** via the Agent tool (`subagent_type: aide-spec-writer`). The delegation prompt MUST:
  - **Name the operation type explicitly** — `session.aide CREATE` or `session.aide UPDATE`. The spec-writer's default operation is `.aide` frontmatter; naming the operation is mandatory for correct dispatch.
  - **Quote all supplied content verbatim** — the spec-writer transcribes; it never invents.
- [ ] **Receive the verdict** — CREATED, UPDATED, or REFUSED.
  - **REFUSED on `session.aide already exists`** → the orchestrator should have called UPDATE. Switch the operation type and re-delegate.
  - **REFUSED on `session.aide missing`** → the orchestrator should have called CREATE. Switch and re-delegate.
  - **REFUSED on ambiguous prompt or missing load-bearing content** (feature intent, `## Where this cycle stopped`) → gather the missing content from conversation context (or ask the user if it's not in context) and re-delegate.
- [ ] **Confirm completion** and continue with whatever pipeline work was in flight before the handoff was invoked.

## What this skill does NOT do

- It does not interview the user. The invoking agent holds the conversation context; this skill only formats it into a delegation prompt for `aide-spec-writer`.
- It does not apply the Brevity Contract. `session.aide` is operational state, not durable intent — no caps on description, no word counts, no forbidden-content rules from the `.aide` body. The Brevity Contract is `.aide`-only.
- It does not delete `session.aide`. That belongs to `aide-maintainer` at feature close.
- It does not bypass `aide-spec-writer`. The skill is the orchestration layer; the agent owns the file write. Calling `Write`/`Edit` on `.aide/session.aide` directly from this skill violates the delegation pattern.

---
> Source: [aidemd-mcp/server](https://github.com/aidemd-mcp/server) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
