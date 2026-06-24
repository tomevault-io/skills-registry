---
name: governancestart-agent-session
description: Starts a new agent session record with deterministic naming. Use when this capability is needed.
metadata:
  author: venikman
---

# Governance: Start Agent Session

## 1. Context

This skill starts a new agent session record under a bounded context, establishing a deterministic session anchor for follow-on handoffs and completion events.

## 2. Inputs

- **context** (required): Bounded context name (safe path segment).
- **session_id** (required): Unique session identifier (safe path segment).
- **capability_ref** (optional): Path to a capability declaration (defaults to the context default capability file).
- **title** (required): Short session title.
- **purpose** (optional): One-paragraph purpose/summary.
- **initiated_by** (optional): Initiator reference (person/team/system).
- **agent_type** (optional): Agent type (strategist, executor, etc).
- **agent_model** (optional): Agent model identifier.
- **role_assignment** (optional): RoleAssignment for U.Work logging (default: Strategist).
- **decisions** (optional): Semicolon-delimited DRR ids/paths.
- **timestamp_start** (optional): ISO-8601 timestamp for deterministic output.

## 3. Outputs

- `runtime/contexts/<context>/sessions/<session_id>.session.md`

## 4. Procedure

1. Validate inputs and ensure safe path segments.
2. Resolve and validate the capability declaration reference.
3. Create the sessions directory if missing.
4. Write the session record with status and timestamps.
5. Emit U.Work via `telemetry/log-work` when available.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/venikman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
